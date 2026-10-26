# Laboratorio Ansible + Docker

Cinco servidores Ubuntu (contenedores con SSH) automatizados con Ansible.

## Archivos
- `Dockerfile`: Ubuntu 22.04 con OpenSSH, usuario `ansible` (clave `ansible`) con sudo sin password.
- `docker-compose.yml`: 5 servicios `server1`..`server5` (SSH en puertos 2201-2205 del host; IPs 172.28.0.11-15).
- `inventario.ini` y `ansible.cfg`: inventario y configuración (ignora la advertencia de host key SSH).
- `playbook.yml`: actualiza APT, crea usuario `itla`, carpeta `app`, `hola.txt` e instala `cowsay` y `htop`. Es idempotente.

## Requisitos
1. Docker (Docker Desktop en Windows).
2. Windows: instalar WSL con `wsl --install` (en PowerShell como administrador) y reiniciar.
3. En Ubuntu/WSL: `sudo apt update && sudo apt install -y ansible sshpass`
   (`sshpass` es necesario para autenticar con contraseña).
4. Habilitar la integración de WSL en Docker Desktop, o clonar el repo dentro de WSL.

## Ejecución
```bash
git clone https://github.com/juliorijo/ansible-docker-lab.git
cd ansible-docker-lab
docker compose up -d --build
ansible all -m ping
ansible-playbook playbook.yml
```
> Si Ansible corre en WSL y Docker Desktop publica puertos en Windows, `127.0.0.1` funciona con WSL2 (localhost forwarding). Si no, cambia `ansible_host` por la IP de Windows vista desde WSL.
> Si `/mnt/c` ignora `ansible.cfg` (directorio world-writable), clona el repo dentro de WSL (`~/`).

## Verificación
```bash
ansible all -a "ls -l /home/itla/app"
ansible all -a "cat /home/itla/app/hola.txt"
ansible all -a "which cowsay htop"
ansible all -m command -a "id itla"
ansible-playbook playbook.yml   # segunda ejecución: changed=0 en las tareas de usuario/archivos
```

## Limpieza
```bash
docker compose down --rmi local
```
