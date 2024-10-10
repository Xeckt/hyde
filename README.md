# Hyde
This project is intended as a Content Management System (CMS) for Jekyll static sites. It is meant to enable users to edit a Jekyll site hosted with Github Sites from
a web browser, and provides a permission management interface for multi-user support.

It is composed of a frontend written in Svelte, and a backend written in Rust.

For more documentation, please check the `docs/` folder at the root of the repo.

## Building
Run `build.sh` to build the front and and backend. The output will be assembled into `./target`, run `hyde` to start the binary.

## Running
The executable requires a few environment variables be set, see `default.env` for a full list. You may set them on the system or copy `default.env` to `.env`
and place it in a folder named `hyde-data/` and customize as needed. This directory is used to store configs, the sqlite database, and the Github App private key.

## Developing
We accept contributions, and we'll happily mentor individuals through their contributions. Feel free to ask for help, either through Github, or in the r/TechSupport discord server.

Development is supported on Windows (With [some caveats](https://github.com/r-Techsupport/hyde/issues/6)), MacOS, and Linux.

### Installing tools
To build the backend, you need to have the Rust toolchain installed (see <https://www.rust-lang.org/tools/install>). We currently aim to support the latest stable version of Rust. Once that's installed, `cargo` will automatically download and install the appropriate dependencies for the project when it's first built. The source code for the backend is located in `./backend`, so navigate there 

To build the frontend, you'll need to have the appropriate Javascript tooling installed (See <https://nodejs.org/en/download/package-manager>). This means Node *and* npm. We aim to use the latest stable version of Node.js (20 at the time of writing).

### Populating `hyde-data`
To keep things organized, the config file and other essential data (sqlite database, Github private key) are stored in a folder in the same directory that the code is run from. This directory is relative to the running process's current directory, so for development, the `hyde-data` folder will be located at `./backend/hyde-data`, 

Hyde expects:

- A config file to be located at `hyde-data/.env`, relative to the running process's current directory. This means you'll need to copy `./default.env` from the root of the project into `./backend/hyde-data/.env`, and fill out any necessary values. Guides for obtaining these values can be found under `./docs`. Configuration values can also be passed with command line arguments, run the executable with the `--help` flag to see more.

- `hyde-data/key.pem`, the private key linked to your Github Application deployment.
- `hyde-data/data.db` - If not created, this file will be automatically created and stores permissions and user account info.

### Running the project in development mode
You can run the backend with `cargo run` from the `backend` folder. This will compile and launch a debug executable, listening on port 8080.

Once the backend is running, in a separate terminal window, run `npm run dev` from the `frontend` folder to start the frontend, listening on `localhost:5173`, viewable from your web browser.

It's recommended that you configure your `rust-analyzer` installation to run `clippy` instead of `check`. See <https://users.rust-lang.org/t/how-to-use-clippy-in-vs-code-with-rust-analyzer/41881/2> for a guide, set `Check On Save: Command` to `clippy`. At the very least, run `cargo clippy` before committing to make sure your code passes lint.


### Building the project in release mode (production)
While build scripts exist for Windows (`./build.ps1`) and *nix (`./build.sh`), you may find it helpful to understand what a production build of Hyde looks like.

Build scripts should assemble the final product into `./target`. It consists of:

- `hyde`(`.exe`): The actual executable, built by running `cargo run --release`. The generated executable is copied from `./backend/target/release/[HYDE-EXECUTABLE-NAME]` to `./target/hyde`(`.exe`). This executable will serve the frontend files stored in `web`, so there's only a single process running. It listens on port `8080` by default, but this is configurable via the `-p`/`--port` command line option.
- `web`: Svelte is configured to compile the frontend into a collection of static files, which can be generated by running `npm run build` from `./frontend`. Those are copied from `./target/frontend/build/` into `./target/web/`. In that directory, you'll find the relevant HTML, CSS, JavaScript, and any assets used on the site. Svelte is also configured to include Brotli and Gzipped versions of those files to reduce bundle size.

There are two ways to copy `hyde-data` folder from `./backend/` to `./target`. First is to manually move the folder, second is to use the build scripts to do it for you:

- Linux: `build.sh -c <path_to_hyde_data>`

- Windows `build.ps1 -C <path_to_hyde_data>` 

Certain behaviors do differ between development and production builds, so be aware of them. Notably, in release mode, the application requires `https` to function.

Hyde's logging can be configured by setting the `RUST_LOG` environment variable or using the `-v`/`--verbosity` command line flag. Possible values are: `TRACE`, `DEBUG`, `INFO`, `WARN`, `ERROR`, and `OFF`.

Upon startup, Hyde will attempt to open a wiki repository found at `./repo` (relevant to the path of the running executable), and pull any upstream changes, or clone the repo into `./repo` if no repo was detected there. The final product can be found by navigating to <http://localhost:8080> in your web browser.

### Building a containerized version of the project
This does not require that you have language tooling installed (Rust, JavaScript), only requiring an OCI implementation of your choice.

Docker:
```sh
docker build -t hyde .
```

Podman:
```sh
podman build -t hyde .
```

## Testing
To run the backend tests, navigate to `./backend`, and run `cargo test`.

To run the frontend tests, navigate to `./frontend` and run `npm test`, or `npm test:watch` for hot reload.
