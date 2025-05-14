 #!/bin/bash

# Colours
red="\033[0;31m"
green="\033[0;32m"
blue="\033[0;34m"
endcolor="\033[0m"

#bundle=1

function ctrl_c() {
  echo -e "\n\n${red}[!] Saliendo...${endcolor}"
  exit 1
}

#Ctrl+C
trap ctrl_c INT

main_url="https://htbmachines.github.io/bundle.js"

function help() {
  echo -e "\n ${green}[+] Uso:${endcolor}"
  echo -e "\t ${blue}u${endcolor}) Descargar o actualizar archivos necesarios"
  echo -e "\t ${blue}m${endcolor}) Buscar máquina por nombre"
  echo -e "\t ${blue}y${endcolor}) Obtener link de la resolución de la máquina (Youtube) "
  echo -e "\t ${blue}i${endcolor}) Buscar máquina por IP "
  echo -e "\t ${blue}o${endcolor}) Buscar por sistema operativo "
  echo -e "\t ${blue}d${endcolor}) Buscar dificultad de la máquina "
  echo -e "\t ${blue}s${endcolor}) Buscar skills "
  echo -e "\t ${blue}h${endcolor}) Mostrar este panel de ayuda"
  echo -e "\n ${green}[+] Todas las búsquedas son (case-sensitive)${endcolor}"
}

bundle_check() {
  checker=$(ls ./bundle.js &> /dev/null ; echo $?)

  if [[ "$checker" -gt 0 ]]; then
    echo -e "\n${red}[!] No existe el fichero bundle.js, por favor actualice con -u antes de trabajar con cualquiera de las funciones.${endcolor} \n"

    echo -e "${blue}[?] ¿Desea descargar el fichero ahora?[y/n] ${endcolor}"
    read resp

    while ! [[ "$resp" =~ ^[YyNn]$ ]]; do
      echo -e "${red}Debes seleccionar 'Y' o 'N'${endcolor} " 
      read resp
    done

    if [[ "$resp" =~ ^[Yy]$ ]]; then

      UpdateFiles

    else
      echo -e "${red}[!] Saliendo ...${endcolor}"
      exit 1
    fi

  fi
}

# Comprobamos que bundle.js exista antes del programa

function searchMachine() {
  machineName="$1"

  checker="$(cat bundle.js | awk "/name: \"$machineName\"/,/resuelta:/" | grep -vE "id:|sku:|resuelta" | tr -d '"' | tr -d ',' | sed 's/^ *//')"

  if [ "$checker" ]; then

    echo -e "\n${green}[+] Listando las propiedades de la máquina $machineName${endcolor}"

    cat bundle.js | awk "/name: \"$machineName\"/,/resuelta:/" | grep -vE "id:|sku:|resuelta" | tr -d '"' | tr -d ',' | sed 's/^ *//'

  else

    echo -e "\n${red}[!] La máquina $machineName no está en la lista${endcolor}"

  fi

}

function searchIP() {
  ipAddress="$1"

  machineName="$(cat bundle.js | grep "ip: \"$ipAddress\"" -B 3 | grep "name: " | awk 'NF{print $NF}' | tr -d '"' | tr -d ',')"


  if [ "$machineName" ]; then

    echo -e "\n${green}La máquina para la IP $1 es $machineName${endcolor}"

    cat bundle.js | awk "/name: \"$machineName\"/,/resuelta:/" | grep -vE "id:|sku:|resuelta" | tr -d '"' | tr -d ',' | sed 's/^ *//'

  else

    echo -e "\n${red}[!] La dirección IP $ipAddress no está en la lista${endcolor}"

  fi

}


function getYouTubeLink() {
  machineName=$1

  youtubeLink="$(cat bundle.js | awk "/name: \"$machineName\"/,/resuelta:/" | grep -vE "id:|sku:|resuelta" | tr -d '"' | tr -d ',' | sed 's/^ *//' | grep youtube | awk 'NF{print $NF}')"


  if [ "$youtubeLink" ]; then

    echo -e "\n${green}La resolución para esta máquina está en $youtubeLink${endcolor}"

  else

    echo -e "\n${red}[!] La máquina $machineName no está en la lista${endcolor}"

  fi

}

function searchDificulty() {
  difficulty="$1"

  checker="$(cat bundle.js | grep "dificultad: \"$difficulty\"" -B 5 | grep name | awk 'NF{print $NF}' | tr -d '"' | tr -d ',')"

  if [ "$checker" ]; then

    echo -e "\n${green}Máquinas con la dificultad $difficulty: ${endcolor}"
    cat bundle.js | grep "dificultad: \"$difficulty\"" -B 5 | grep "name: " | awk 'NF{print $NF}' | tr -d '"' | tr -d ',' | column

  else

    echo -e "\n${red}[!] No existe la dificultad $difficulty${endcolor}"

  fi

}


function UpdateFiles() {
  tput civis

  if [ ! -f bundle.js ]; then

    echo -e "\n ${green}[+] Descargando todos los archivos necesarios...${endcolor}"
    curl -s $main_url > bundle.js
    js-beautify bundle.js | sponge bundle.js
    echo -e "\n ${green}Todos los archivos han sido descargados${endcolor}"
  else

    echo -e "\n ${green}[+] Comprobando si hay actualizaciones...${endcolor}"
    sleep 1

    curl -s $main_url > bundle_temp.js
    js-beautify bundle_temp.js | sponge bundle_temp.js

    md5_temp_value=$(md5sum bundle_temp.js | awk '{print $1}')
    md5_original_value=$(md5sum bundle.js | awk '{print $1}')

    if [[ "$md5_temp_value" == "$md5_original_value" ]]; then

      echo -e " ${green}[+] No se han detectado actualizaciones\n${endcolor}"
      rm bundle_temp.js

    else

      echo -e " ${green}[+] Hay actualizaciones, actualizando...\n${endcolor}"
      sleep 1
      cp bundle_temp.js bundle.js && rm bundle_temp.js

    fi

  fi

  tput cnorm
}


function getOSMachines() {
  os="$1"

  os_results="$(cat bundle.js | grep "so: \"$os\"" -B 5 | grep "name: " | awk 'NF{print $NF}' | tr -d ',' | tr -d '"' | column)"

  if [ "$os_results" ]; then
    echo -e "\n${green}[+] Mostrando las máquinas cuyo sistema operativo es $os${endcolor}"
    echo -e "\n $os_results"

  else
    echo -e "\n${red}[!] El sistema $os no existe${endcolor}"

  fi

}


function getOSDifficiltyMachines() {
  difficulty="$1"
  os="$2"

  checker="$(cat bundle.js | grep "so: \"$os\"" -C 4 | grep "dificultad: \"$difficulty\"" -B 5 | grep "name: " | awk 'NF{print $NF}' | tr -d '"' | tr -d ',' | column)"


  if [ "$checker" ]; then
    echo -e "\n ${green}[+] Listando máquinas con la dificultad $difficulty y el sistema operativo $os ${endcolor}"
    cat bundle.js | grep "so: \"$os\"" -C 4 | grep "dificultad: \"$difficulty\"" -B 5 | grep "name: " | awk 'NF{print $NF}' | tr -d '"' | tr -d ',' | column

  else
    echo -e "\n ${red}[+] Se ha indicado una dificultad o un sistema operativo incorrecto${endcolor}"

  fi

}


function getSkill() {
  skill="$1"

  check_skill="$(cat bundle.js | grep "skills: " -B 6 | grep -i "$skill" -B 6 | grep "name: " | awk 'NF{print $NF}' | tr -d '"' | tr -d ','  | column)"

  if [ "$check_skill" ]; then
    echo -e "\n ${green}[+] Listando máquinas que tienen $skill${endcolor}"
    cat bundle.js | grep "skills: " -B 6 | grep -i "$skill" -B 6 | grep "name: " | awk 'NF{print $NF}' | tr -d '"' | tr -d ','  | column
  else
    echo -e "\n ${red}[!] No se ha encontrado ninguna máquina con la Skill $skill${endcolor}"
  fi
}

# Indicadores
declare -i parameter_counter=0


# Chivatos
declare -i chivato_difficulty=0

declare -i chivato_os=0

while getopts "m:ui:y:d:o:s:h" arg; do
  case $arg in
    m) machineName="$OPTARG"; let parameter_counter+=1;;
    u) let parameter_counter+=2;;
    i) ipAdress="$OPTARG"; let parameter_counter+=3;;
    y) machineName="$OPTARG"; let parameter_counter+=4;;
    d) difficulty="$OPTARG"; let chivato_difficulty=1; let parameter_counter+=5;;
    o) os=$OPTARG; let chivato_os=1; let parameter_counter+=6;;
    s) skill="$OPTARG"; let parameter_counter+=7;;
    h) ;;
  esac
done




if [ $parameter_counter -eq 1 ]; then
  bundle_check
  searchMachine "$machineName"

elif [ $parameter_counter -eq 2 ]; then
  UpdateFiles

elif [ $parameter_counter -eq 3 ]; then
  bundle_check
  searchIP "$ipAdress"

elif [ $parameter_counter -eq 4 ]; then
  bundle_check
  getYouTubeLink "$machineName"

elif [ $parameter_counter -eq 5 ]; then
  bundle_check
  searchDificulty "$difficulty"

elif [ $parameter_counter -eq 6 ]; then
  bundle_check
  getOSMachines "$os"

elif [ $parameter_counter -eq 7 ]; then
  bundle_check
  getSkill "$skill"

elif [ $chivato_difficulty -eq 1 ] && [ $chivato_os -eq 1 ]; then
  bundle_check
  getOSDifficiltyMachines "$difficulty" "$os"

else
  help
fi
