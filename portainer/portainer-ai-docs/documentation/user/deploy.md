# Deploy

Deploy is how applications get onto Kubernetes in Portainer-Run. Drop in the files Claude, Cursor, or any other AI coding tool generated, and Portainer-Run handles runtime detection, dependency installation, Git commit, and Kubernetes deployment automatically. No Dockerfile, no CI pipeline, no container registry, and no platform engineering ticket.

## Two ways to provide source

**File upload**: upload or drag in the files your AI tool produced. You can add a project folder, individual files, or a ZIP archive. ZIP archives are extracted in your browser, and if an archive can't be read, Portainer-Run shows the error rather than dropping the files. Portainer-Run commits the files to your configured Git target before deploying. A single deploy or source update can carry up to 8 MiB of source.

<figure><img src="../.gitbook/assets/deploy-upload-files.png" alt=""><figcaption></figcaption></figure>

**Existing Git repository**: instead of uploading files, select a configured [Git target](admin/git-targets.md), branch, and optional subfolder. Portainer-Run fetches the file listing from that repository, detects the runtime, and clones directly from the source repository on every pod start. In this mode, no source files are additionally committed to the manifests repository; only the manifest itself is.

<figure><img src="../.gitbook/assets/deploy-git-repo.png" alt=""><figcaption></figcaption></figure>

## Runtime detection

The runtime is detected from the file structure. Detection runs in priority order; the first match wins.

| Signal                                                     | Runtime               |
| ---------------------------------------------------------- | --------------------- |
| `package.json` present                                     | Node.js 22            |
| `requirements.txt` present, or any `.py` file              | Python 3.13           |
| `Gemfile` present, or any `.rb` file                       | Ruby 3.4              |
| Any `.php` file                                            | PHP 8.4 (Apache)      |
| All files are static assets (HTML, CSS, JS, images, fonts) | nginx                 |
| No match                                                   | nginx (safe fallback) |

If your uploaded files include a `.env.example`, Portainer-Run detects it and presents an editable list of keys before deploying. Keys matching common secret patterns (`SECRET`, `TOKEN`, `KEY`, `PASSWORD`) are masked in the form.

## What happens on deploy

Up to four init containers run in sequence before the application starts, each conditional on what's needed:

1. **vibe-web-guard**: present for PHP and static (nginx) applications. Adds a web server rule that refuses requests for any dotfile or dot directory in the web root (such as `.env`, `.git`, or `.htpasswd`), except `/.well-known/`, whatever start command the application uses.
2. **vibe-sync**: always present. Clones the source files from Git into a PersistentVolume. The repository's `.git` directory is removed before the files are copied, so it never ends up in the application's directory.
3. **vibe-install**: present for Node.js, Python, Ruby, and PHP. Runs the runtime's dependency installer (`npm install`, `pip install`, `bundle install`, or `composer install`) against the PVC. Skipped entirely for nginx and static deployments.
4. **vibe-env**: writes a `.env` file to the PVC from the configured environment variables, so the application works whether or not it uses a dotenv library itself. Present only when environment variables are configured, except for static (nginx) applications: nginx would serve a `.env` file, so they never get one, and this step instead removes any `.env` an earlier version left behind.

Every application, including static ones, also receives its environment variables in the container environment.

{% hint style="info" %}
Applications deployed before these protections were added pick them up the next time they are redeployed.
{% endhint %}

None of this requires a build step. Once the init containers finish, the main application container starts against the pre-populated PVC using a stock public runtime image, with a default resource request of 0.1 CPU / 1 GB memory and a limit of 1 CPU / 4 GB memory applied automatically. See [Applications](applications.md) for how to adjust this afterward.

The manifest and (for file-upload deployments) the source files are committed to your Git target, and Portainer reconciles the change onto the cluster the same way it does for every deployment. See [Architecture](https://app.gitbook.com/s/GwxJJZcdHkdw08WBsjrl/architecture) for the full flow, and [Git Targets](admin/git-targets.md) for where things land in the repository.

## Updating a deployed app

How you update an application depends on how its source was provided:

* **File upload**: from the application's **Edit** tab, use **Upload updated files** to select a folder, files, or a ZIP archive, then click **Commit & Restart** to commit the change to Git and redeploy.
* **Existing Git repository**: push your changes to the source repository, then restart the application. The source is cloned when the pod starts, so a restart is what picks up the new commits. **Upload updated files** isn't available for these applications, since Portainer-Run never writes to your source repository.

The PersistentVolume retains its state across pod restarts, so anything the application wrote to disk, including uploaded files and user data, persists through the update. See [Applications](applications.md) for the full Edit tab reference.

## Current limitations

* **Single-container only.** There's no support for multi-service architectures such as sidecars, databases, or message queues.
* **No compiled languages.** Go, Java, Rust, .NET, and anything else requiring a build step before running are out of scope. The init containers install runtime dependencies; they don't compile source code.
* **An Ingress controller is required** for Ingress exposure to actually work. Portainer-Run can create the Ingress resource regardless, but it doesn't install or configure a controller.
* **A default StorageClass is required.** Deploy creates a PVC for every application to hold source files and state. Without a default StorageClass, the PVC stays unbound and the application won't start.

### Deploying without the UI: MCP

Everything Deploy does in the browser is also available through Portainer-Run's [MCP endpoint](mcp.md), so an AI coding tool can deploy an application it just generated without a human ever opening Portainer-Run at all.
