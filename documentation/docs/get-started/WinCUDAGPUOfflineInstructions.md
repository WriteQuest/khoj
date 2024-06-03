by WriteQuest w/ PT and debanjum

# Windows 10 1080Ti CUDA 12.1 Self-Host Install Instructions
[Khoj Base Docs](https://docs.khoj.dev/get-started/setup)
>Commands ran in PowerShell and Command Prompt unless otherwise stated. 
>*I **tried**to remember where CP was used, but I **know** I missed a section, it may have been the pyvector docker portion*
## Prereqs List
- CUDA 12.1
- git
- cmake
- Visual Studio Community with:
  - Desktop development with C++
  - Python development
  - Linux embedded development with C++

ENVIROMENTAL PATH's that may/may not needed
```shell
C:\Program Files\Docker\Docker\resources\bin
C:\Program Files\PostgreSQL\16\bin
C:\Program Files\CMake\bin
C:\Program Files\Git\cmd
C:\Users\House\AppData\Local\Packages\PythonSoftwareFoundation.Python.3.12_qbz5n2kfra8p0\LocalCache\local-packages\Python312\Scripts
```
## Good luck!

### Install Python 
>(Use Windows Store) - Will also install pip!

### Install [PostgreSQL](https://www.postgresql.org/download/windows/)
>StackBuilder - is not needed.

Create Khoj database.
```shell
	createdb -U postgres khoj --password
```

### Install [Docker](https://www.docker.com/get-started/)

>**ERROR FIX**
>Something Something

>Use *COMMAND PROMPT* here.
```shell
	call "C:\Program Files\Microsoft Visual Studio\2022\Community\VC\Auxiliary\Build\vcvars64.bat"
```
### Install PgVector
[docs](https://github.com/pgvector/pgvector?tab=readme-ov-file#installation-notes---windows)
```shell
	docker pull pgvector/pgvector:pg16
	git clone --branch v0.7.0 https://github.com/pgvector/pgvector.git
	cd pgvector
	docker build --pull --build-arg PG_MAJOR=16 -t myuser/pgvector .
```

Enable pgVector in database.
>Use postgres *pgAdmin* to access khoj database and run query:
	CREATE EXTENSION vector;
	
#### Install correct pyTorch version
```shell
	pip install torch==2.2.2 torchvision==0.17.2 torchaudio==2.2.2 --index-url https://download.pytorch.org/whl/cu121
```
#### Install llama-cpp-python
[docs](https://python.langchain.com/v0.1/docs/integrations/llms/llamacpp/#installation)
>**ERROR FIX** 
>(Nividia CUDA 12.1 dosen't like MVS version >17.0))
>So we force Nividia to play with Microsoft
```shell
	$env:NVCC_APPEND_FLAGS='--allow-unsupported-compiler'
```
Active cuda settings.
```shell
	$env:CMAKE_ARGS="-DLLAMA_CUDA=on"
```
Make sure to make the make.
```shell
	$env:FORCE_CMAKE=1
```
Version supported by Khoj, alt you can git with a branch tag.
>may need `pip cache purge` to assure it dosen't use wrong version
```shell
	pip install llama-cpp-python==0.2.64
```
>**ERROR FIX**
>(long paths) [docs](https://pip.pypa.io/warnings/enable-long-paths)
```shell
	New-ItemProperty -Path "HKLM:\SYSTEM\CurrentControlSet\Control\FileSystem" `
	-Name "LongPathsEnabled" -Value 1 -PropertyType DWORD -Force
```
Confirm happy CUDA/Llama.
```shell
	python -m torch.utils.collect_env
```
#### Install Khoj
```shell
	$env:CMAKE_ARGS = "-DLLAMA_OPENBLAS=on"
	python -m pip install khoj-assistant
```
Firsttime Running Khoj Set Env Vars
```shell
	$env:KHOJ_ADMIN_EMAIL="something@somewhere.com"
	$env:KHOJ_ADMIN_PASSWORD="password"
```
Run
```shell
	khoj --anonymous-mode
```
