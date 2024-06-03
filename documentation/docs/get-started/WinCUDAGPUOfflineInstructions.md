by WriteQuest and PT w/ debanjum

#Windows 10 1080Ti CUDA 12.1 Self-Host Install Instructions
#KHOJ BASE DOCS 
	[https://docs.khoj.dev/get-started/setup]

##Prereqs List
	-CUDA 12.1
	-git
	-cmake
	-Visual Studio Community with:
		--Desktop development with C++
		--Python development
		--Linux embedded development with C++

##ENVIROMENTAL PATH's may/may not needed
C:\Program Files\Docker\Docker\resources\bin
C:\Program Files\PostgreSQL\16\bin
C:\Program Files\CMake\bin
C:\Program Files\Git\cmd
C:\Users\House\AppData\Local\Packages\PythonSoftwareFoundation.Python.3.12_qbz5n2kfra8p0\LocalCache\local-packages\Python312\Scripts

#Good luck!

##Install Python 
(Use Windows Store) - Will also install pip!

##Install PostgreSQL
https://www.postgresql.org/download/windows/
	StackBuilder - is not needed.

##Install Docker -https://www.docker.com/get-started/

#ERROR FIX
	#USE COMMAND PROMPT
	call "C:\Program Files\Microsoft Visual Studio\2022\Community\VC\Auxiliary\Build\vcvars64.bat"

#Install PgVector https://github.com/pgvector/pgvector?tab=readme-ov-file#installation-notes---windows
	docker pull pgvector/pgvector:pg16
	git clone --branch v0.7.0 https://github.com/pgvector/pgvector.git
	cd pgvector
	docker build --pull --build-arg PG_MAJOR=16 -t myuser/pgvector .
	cd ../

#Create Khoj Database
	createdb -U postgres khoj --password

#Enable PgVector in postgres khoj DATABASE (run query)
	#IN pgAdmin, right click khoj, run query
	CREATE EXTENSION vector;
	
#Install correct pyTorch version
	pip install torch==2.2.2 torchvision==0.17.2 torchaudio==2.2.2 --index-url https://download.pytorch.org/whl/cu121

#INSTALL COMMANDS
	$env:CMAKE_ARGS = "-DLLAMA_OPENBLAS=on"
	python -m pip install khoj-assistant

#Install llama-cpp-python		
	#ERROR FIX INFO (llama w/ gpu): - https://python.langchain.com/v0.1/docs/integrations/llms/llamacpp/#installation
	#ERROR FIX INFO  (unsupported compiler with WVC++ (Version mismatching, ignore))

	#turn on the cuda
	$env:CMAKE_ARGS="-DLLAMA_CUDA=on"
	#make the damn thing or else!
	$env:FORCE_CMAKE=1
	#To allow MVC++ 17.0+ to work with cuda12.1
	$env:NVCC_APPEND_FLAGS='--allow-unsupported-compiler'
	#version supported by Khoj pulled, alt you can git with a branch tag, may need ( or pip cache purge)
	pip install llama-cpp-python==0.2.64

#ERROR FIX INFO (long paths): https://pip.pypa.io/warnings/enable-long-paths
	#Reg Key Entry via Powershell
	New-ItemProperty -Path "HKLM:\SYSTEM\CurrentControlSet\Control\FileSystem" `
	-Name "LongPathsEnabled" -Value 1 -PropertyType DWORD -Force

#Confirm Happy CUDA/Llama
	python -m torch.utils.collect_env

#Firsttime Run Khoj Env Set
	$env:KHOJ_ADMIN_EMAIL="something@somewhere.com"
	$env:KHOJ_ADMIN_PASSWORD="password"
#Run
	khoj --anonymous-mode
