#!/data/data/com.termux/files/usr/bin/bash
set -e

NAME="Windows11"
RAM="8G"
CPU="4"
DISK="$HOME/windows11.qcow2"
ISO="$HOME/windows11.iso"

echo "=== PC VIRTUAL WINDOWS 11 ==="

pkg update -y
pkg install -y qemu-utils qemu-system-x86-64-headless

if [ ! -f "$DISK" ]; then
    echo "Criando disco virtual de 100 GB..."
    qemu-img create -f qcow2 "$DISK" 100G
fi

if [ ! -f "$ISO" ]; then
    echo "ERRO: windows11.iso não encontrada."
    echo "Coloque a ISO na pasta HOME do Termux."
    exit 1
fi

echo "Iniciando PC virtual..."
echo "RAM: $RAM"
echo "CPU: $CPU"
echo "DISCO: 100 GB"
echo "VNC: localhost:5901"

qemu-system-x86_64 \
-name "$NAME" \
-machine q35 \
-cpu max \
-smp "$CPU" \
-m "$RAM" \
-drive file="$DISK",format=qcow2 \
-cdrom "$ISO" \
-boot menu=on \
-device virtio-vga \
-device virtio-net-pci,netdev=net0 \
-netdev user,id=net0 \
-device ich9-intel-hda \
-audiodev driver=none,id=audio0 \
-vnc 127.0.0.1:1 \
-usb \
-device usb-tablet \
-no-reboot

echo "PC virtual encerrado."
