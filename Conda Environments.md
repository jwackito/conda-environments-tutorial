# Conda environments para todes
- ## Introdución
	- Paso a paso para instalar mi setup de Data Analysis ~~Conda Environments~~ como a mi me gusta. Esto incluye `Conda`, `Jupyter Hub/Lab` en modo servidor para varios usuarios, los `drivers de nvidia` si tenes GPUs discretas para hacer cómputo, algunas herramientas para monitorear el estado del sistema y ver que ande todo (`htop`, `nvtop`).
		- Based on this two awesome (but a bit outdated) tutorials
		- [Definitive Guide to Python Virtual Environments](https://www.whiteboxml.com/blog/the-definitive-guide-to-python-virtual-environments-with-conda)
		- [The Definitive Data Scientist Environment Setup](https://www.whiteboxml.com/blog/the-definitive-data-scientist-environment-setup)
	- Pasos para Ubuntu 24.04 que deberían ser similares para otras distros.
	- Haré uso indistintamente de Anaconda, Miniconda y Conda, pero son cosas distintas. El tutorial ayuda a instalar Miniconda 3 y a usar Conda como gestor de environments.
	- Para todo el tutorial, asumo lo siguiente:
		- Estas usando bash. A pesar que hoy en día existe [otros shells más potentes](https://www.redswitches.com/blog/types-of-linux-shells/#8friendlyinteractiveshellfish) y que `conda` se integra bien con la mayoría de ellos, nunca he tenido tiempo de aprender a usarlos. Si seguis estos pasos usando otro shell y encontras diferencias, por favor submitea un pull request.
		- `$HOME` hace referencia al home del usuario, normalmente `/home/<nombre_de_usuario>`. Esta es una variable de entorno definida en Linux que puede verse con el comando `echo $HOME`.
		- `$MINI3HOME` hace referencia al directorio de instalación de Miniconda 3, normalmente `$HOME/miniconda3`. Al contrario, esta no es una variable de entorno definida en Linux, sino solo una referencia a la carpeta donde se instala miniconda con el propósito de este tutorial.
- ## Instalar miniconda 3
  collapsed:: true
	- Ir a [la doc de miniconda](https://docs.anaconda.com/miniconda/) y bajar la última versión del script de instalación. Normalmente disponible en el enlace
		- [Miniconda3-latest-Linux-x86_64.sh](https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh)
	- ```bash
	  wget https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh
	  bash Miniconda3-latest-Linux-x86_64.sh
	  ```
	- Lo primero es leer y aceptar la licencia.
	- Luego script pregunta donde se desea instalar miniconda. En mi caso dejé la opción por defecto, `$HOME/miniconda3` pero podría ser cualquier otra carpeta. Sea cual sea la que hayas elegido, a partir de ahora voy a llamarlo `$MINI3HOME`.
	- Una vez que se ha instalado todo, el script pregunta si se desea que conda active el entorno por defecto, `base`, cada vez que arranca el shell. Esto básicamente escribe una sección nueva en el archivo `$HOME/.bashrc`, que gobierna como se comporta bash al iniciar la sesión para un usuario. Yo soy bastante celoso de mi `.bashrc`, por eso entiendo que puedas querer contestar que no, pero es seguro decir que si. Lo que va a hacer es iniciar el environment `base` cada vez que arranque una sesión en la terminal, vayas a usar conda o no. La sección que se agrega tiene la pinta de lo que sigue
	- ```bash
	  # >>> conda initialize >>>
	  # !! Contents within this block are managed by 'conda init' !!
	  __conda_setup="$('/home/<username>/miniconda3/bin/conda' 'shell.bash' 'hook' 2> /dev/null)"
	  if [ $? -eq 0 ]; then
	      eval "$__conda_setup"
	  else
	      if [ -f "/home/<username>/miniconda3/etc/profile.d/conda.sh" ]; then
	          . "/home/<username>/miniconda3/etc/profile.d/conda.sh"
	      else
	          export PATH="/home/<username>/miniconda3/bin:$PATH"
	      fi
	  fi
	  unset __conda_setup
	  # <<< conda initialize <<<
	  ```
	- Si pusiste que si, entonces listo. Al abrir una nueva sesión de shell (o una nueva pestaña del terminal) deberías ver `(base)` en el prompt.
	- ```
	  (base) <username>@<host>:~$
	  ```
	- Esto quiere decir que `conda` esta activo y funcionando, y que se cargó el environment `base` que por defecto es un environment mínimo del cual todos los demás environments heredan.
	- ```
	  (base) <username>@<host>:~$ conda
	  usage: conda [-h] [-v] [--no-plugins] [-V] COMMAND ...
	  
	  conda is a tool for managing and deploying applications, environments and packages.
	  
	  options:
	    -h, --help          Show this help message and exit.
	    -v, --verbose       Can be used multiple times. Once for detailed output, twice for INFO logging, thrice for DEBUG logging, four times for TRACE logging.
	    --no-plugins        Disable all plugins that are not built into conda.
	    -V, --version       Show the conda version number and exit.
	  
	  commands:
	    The following built-in and plugins subcommands are available.
	  
	    COMMAND
	      activate          Activate a conda environment.
	      clean             Remove unused packages and caches.
	      compare           Compare packages between conda environments.
	      config            Modify configuration values in .condarc.
	      content-trust     Signing and verification tools for Conda
	      create            Create a new conda environment from a list of specified packages.
	      deactivate        Deactivate the current active conda environment.
	      doctor            Display a health report for your environment.
	      export            Export a given environment
	      info              Display information about current conda install.
	      init              Initialize conda for shell interaction.
	      install           Install a list of packages into a specified conda environment.
	      list              List installed packages in a conda environment.
	      notices           Retrieve latest channel notifications.
	      package           Create low-level conda packages. (EXPERIMENTAL)
	      remove (uninstall)
	                        Remove a list of packages from a specified conda environment.
	      rename            Rename an existing environment.
	      repoquery         Advanced search for repodata.
	      run               Run an executable in a conda environment.
	      search            Search for packages and display associated information using the MatchSpec format.
	      update (upgrade)  Update conda packages to the latest compatible version.
	  ```
	- El comando `conda info` muestra información acerca del environment que está activo y los paths a los archivos de configuración por defecto. Esto es super importante, sobre todo la ubicación del archivo `.condarc`, el path para los environments por defecto, etc.
- ## Creando y activando environments en `conda`
	- `conda` instala por defecto un enviroment que se llama `base`. De hecho, `conda` está instalado en este environment como una librería más. Si bien es posible instalar paquetes y librerías en este environment, no puedo dejar de expresar mi más profundo apoyo al consejo del tutorial de [WhiteBox](https://www.whiteboxml.com/blog/the-definitive-guide-to-python-virtual-environments-with-conda)...
		- ```
		  Don't mess with the base environment.
		  ```
	- Primero vamos a crear un nuevo environment llamado mlbase
		- ```
		  conda create -n mlbase
		  ```
	- Esto solo crea el environment pero no lo activa. Para instalar paquetes y librerías en un environment primero debe activarse usando `conda activate`
		- ```
		  conda activate mlbase
		  ```
	- La marca del environment activo del prompt debería cambiar de `(base)` a `(mlbase)`. Todo lo que hagamos a partir de ahora afectará directamente al environment `mlbase` mientras que el environment `base` no será modificado. Para volver al environment `base` basta con ejecutar `conda deactivate` y la marca del environment activo volverá a `(base)`
		- ```
		  conda deactivate
		  ```
	- Para eliminar un environment
- ## Instalando