# Building manylinux2014 wheels

**WARNING:** To cause everything to be rebuilt, this script will delete ALL changes to the
repository, including both changes to tracked files, and ANY untracked files.

It will also cause all files inside the repository to be owned by root, and
produce .whl files owned by root.


Uncomment the following line from `/ray/python/build-wheel-manylinux2014.sh`:
```
export PATH=/root/bazel-3.2.0/output:$PATH:/root/bin
```

Inside the root directory (i.e., one level above this python directory), run

```
docker run -e BUILDKITE="false" -e TRAVIS_COMMIT="test" --rm -w /ray -v `pwd`:/ray -ti quay.io/pypa/manylinux2014_x86_64 /ray/python/build-wheel-manylinux2014.sh
```

The wheel files will be placed in the .whl directory.

## Building MacOS wheels

Before unning the MacOS script you must first `brew install` `npm` (9.8.1), `nvm` (0.39.5),`miniconda` (23.5.2), and `python@3.8`-`python@3.10`.
Note: The current version of Ray has separate scripts for `macos` and `macos-arm64`. These are consolidated in a later version.

If buliding on `macos-64` then you must run the contents of this if-block first (or delete the conditional check):
```
if [[ -n "${SKIP_DEP_RES}" ]]; then
  ./ci/env/install-bazel.sh
  curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.0/install.sh | bash
  curl -o- https://github.com/conda-forge/miniforge/releases/latest/download/Miniforge3-MacOSX-arm64.sh | bash
  conda init bash
  source ~/.bash_profile
  # Use the latest version of Node.js in order to build the dashboard.
  source "$HOME"/.nvm/nvm.sh
  nvm install $NODE_VERSION
  nvm use $NODE_VERSION
fi
```

To build wheels for MacOS, run the following inside the root directory (i.e.,
one level above this python directory).

```
sudo BUILDKITE="false" ./python/build-wheel-macos-arm64.sh
```

The script uses `sudo` multiple times, so you may need to type in a password.
