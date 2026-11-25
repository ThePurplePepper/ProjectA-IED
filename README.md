# ProjectA-IED

Repository to manage all artifacts related to the IED detection project, as part of the Project A course at the Viterbi Faculty of Electrical & Computer Engineering, Technion (SIPL).

---

## Development & Environment Setup

This project supports two workflows:
1. **Remote Development** on the Technion ECE faculty Linux compute cluster (`igum-login1` .. `igum-login10`) via SSH.
2. **Local Development** on a personal machine (Windows / macOS / Linux) using `uv`.

---

### Mode 1: Remote Linux Cluster (SSH)

On the faculty servers, the shared Python environment (`ied_env`), datasets (`data/`), outputs (`outputs/`), and separate repository clones are already configured under `/ugproj/SIPL-prj11074`:

* `/ugproj/SIPL-prj11074/miniforge3/envs/ied_env` — Shared Conda environment (Python 3.11 + CUDA/PyTorch + scientific stack).
* `/ugproj/SIPL-prj11074/repo_yoav` — Yoav's development clone.
* `/ugproj/SIPL-prj11074/repo_yuval` — Yuval's development clone.
* `/ugproj/SIPL-prj11074/repo_main` — Clean `main` clone reserved for running stable Slurm batch jobs.
* `/ugproj/SIPL-prj11074/data` — Shared datasets (excluded from Git).
* `/ugproj/SIPL-prj11074/outputs` — Model checkpoints, logs, and Slurm outputs (excluded from Git).

#### One-Time Setup (For a New Team Member)
If connecting to the cluster for the first time, link your shell to the shared Miniforge installation and register the local package in editable mode:

    /ugproj/SIPL-prj11074/miniforge3/bin/conda init bash
    source ~/.bashrc

    conda activate ied_env
    cd /ugproj/SIPL-prj11074/repo_yuval   # Replace with your repo folder
    git pull origin main
    uv pip install --no-build-isolation --no-deps -e .

#### Daily Remote Workflow
1. Navigate to your personal repository folder and pull the latest changes:

       cd /ugproj/SIPL-prj11074/repo_yoav   # or repo_yuval
       git pull origin main

2. Activate the shared Conda environment:

       conda activate ied_env

   *(Optional)* Add the following lines to your `~/.bashrc` to automate this on every SSH login:

       cd /ugproj/SIPL-prj11074/repo_yoav   # or repo_yuval
       conda activate ied_env
       export UV_CACHE_DIR=/ugproj/SIPL-prj11074/.uv_cache

#### Adding a New Python Dependency (Remote)
Because `ied_env` is shared, installing a package once makes it immediately available to both team members on the cluster. To keep local environments in sync:
1. Install the package into `ied_env`:

       export UV_CACHE_DIR=/ugproj/SIPL-prj11074/.uv_cache
       uv pip install <package-name>

2. Add `<package-name>` to the `dependencies` list in `pyproject.toml`, then commit and push to Git.

#### Updating Production (`repo_main`) Before Slurm Jobs
Always submit heavy training jobs from `repo_main` after merging tested code into `main`:

    cd /ugproj/SIPL-prj11074/repo_main && git pull origin main

---

### Mode 2: Local Development (Windows / macOS / Linux)

To work locally on a personal machine (e.g., for UI/labeling tool development or exploratory analysis):

#### 1. Install `uv` (if not already installed)
* **Windows (PowerShell):**

      powershell -ExecutionPolicy ByPass -c "irm https://astral.sh/uv/install.ps1 | iex"

* **macOS / Linux:**

      curl -LsSf https://astral.sh/uv/install.sh | sh

#### 2. Clone the Repository & Sync Dependencies

    git clone <YOUR_REPO_URL>
    cd ProjectA-IED
    uv sync

`uv sync` automatically creates a local `.venv` with Python 3.11, installs all dependencies listed in `pyproject.toml`, and installs `ied_detector` in editable mode.

#### 3. Activate the Environment / Run Code
* **IDE (PyCharm / VS Code):** Open the `ProjectA-IED` folder; the IDE will automatically detect `.venv` as the project interpreter.
* **Terminal (Windows PowerShell / CMD):**

      .venv\Scripts\activate

* **Terminal (macOS / Linux):**

      source .venv/bin/activate

* **Direct execution without manual activation:**

      uv run python <script.py>

#### 4. Data & Git Hygiene
* Keep all EEG/sEEG data files inside a local `data/` folder (already excluded by `.gitignore`) — never commit raw recordings or model checkpoints (`.pt`, `.ckpt`) to Git.
* Commit and push code changes from your local machine, then run `git pull origin main` in your remote cluster repo (`repo_yoav` / `repo_yuval`) to sync.