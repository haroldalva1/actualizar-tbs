#!/bin/bash

# Eliminar contenido del directorio tbsdrivers
rm -r tbsdrivers/*

# Actualizar el sistema
sudo apt-get update


sudo apt-get full-upgrade -y

# Cambiar al directorio tbsdrivers
cd tbsdrivers/


# Clonar repositorios
git clone https://github.com/tbsdtv/media_build.git
check_error "Fallo al clonar media_build"

git clone --depth=1 https://github.com/tbsdtv/linux_media.git -b latest ./media
check_error "Fallo al clonar linux_media"

# Cambiar al directorio media_build
cd media_build/
check_error "No se pudo cambiar al directorio media_build"

# Compilar
make dir DIR=../media
check_error "Fallo en make dir"

make allyesconfig
check_error "Fallo en make allyesconfig"

make -j4
check_error "Fallo en make -j4"

# Eliminar drivers antiguos e instalar nuevos
sudo rm -rf /lib/modules/$(uname -r)/kernel/drivers/media/*
check_error "Fallo al eliminar drivers antiguos"

sudo make install
check_error "Fallo en make install"

# Descargar e instalar firmwares
cd ..
wget http://www.tbsdtv.com/download/document/linux/tbs-tuner-firmwares_v1.0.tar.bz2
check_error "Fallo al descargar firmwares"

sudo tar jxvf tbs-tuner-firmwares_v1.0.tar.bz2 -C /lib/firmware/
check_error "Fallo al extraer firmwares"

echo "Instalación completada. El sistema se reiniciará en 10 segundos."
echo "Presiona Ctrl+C para cancelar el reinicio automático."

# Esperar 10 segundos antes de reiniciar
sleep 10

# Reiniciar el sistema
sudo reboot
