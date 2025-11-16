<h1>Proyecto Final</h1>
<p>Realizado por: Juan David Bedoya - Amerika Esmeralda Giraldo</p>

<h2>1. Creación de las particiones en virtualBox</h2>
<p>Se crearon los discos de almacenamiento en VirtualBox, exactamente se crearon 9, para crear los 3 RAIDs necesarios en el proyecto, para cada RAID se usaran 3 discos, uno de 1 GB y dos de 2 GB.</p>
<img width="264" height="156" alt="image" src="https://github.com/user-attachments/assets/8af6e4f1-cee5-4fcc-adb1-42227059efef" />
<p>Luego se adjuntaron a la maquina virtual.</p>
<img width="295" height="322" alt="image" src="https://github.com/user-attachments/assets/79e717e3-cf16-4564-9622-1dd434bf970b" />
<p>Por ultimo se verifica en la terminal de la maquina virtual que si se hayan adjuntado exitosamente con el comando 'ls /dev'.</p>
<img width="642" height="289" alt="image" src="https://github.com/user-attachments/assets/9674bb0d-6c98-4caf-815c-ee99217aa32d" />
<p>Se observa que si se adjuntaron exitosamente (sdb, sdc, sdd, ..., sdj).</p>

<h2>2. Creación de los RAIDs</h2>
<h3>2.1. Instalacion de mdadm.</h3>
<p>Para hacer la creacion de los RAIDs necesitamos instalar el mdadm, el cual instalaremos con el comando 'sudo apt install mdadm -y'.</p>
<img width="1330" height="991" alt="image" src="https://github.com/user-attachments/assets/0cf9160c-03b1-48dd-91b0-df55f89d60f8" />
<p>Posterior a la instalacion se ejecuta el comando 'sudo reboot' para reiniciar el sistema y que carguen los recursos adecuadamente.</p>
<h3>2.2. Creación de los RAIDs con las 3 particiones correspondientes.</h3>
<p>Ahora con la mdadm ya instalado podemos crear los RAIDs de la siguiente forma 'sudo mdadm --create /dev/md* --level=1 --raid-devices=3 /dev/sd* /dev/sd* /dev/sd*'.</p>
<img width="1046" height="243" alt="image" src="https://github.com/user-attachments/assets/809ec8d4-a6aa-4ce3-85f6-6cf09c8a4080" />
<img width="1048" height="214" alt="image" src="https://github.com/user-attachments/assets/02065b99-7928-4464-b99e-7a1ddbb9a9ff" />
<img width="1051" height="217" alt="image" src="https://github.com/user-attachments/assets/4a609a96-cd0d-45a1-98e6-6ae288fa02db" />
<p>Ahora verificamos que se hayan creado y que su estado sea activo, con el comando 'cat /proc/mdstat'.</p>
<img width="735" height="263" alt="image" src="https://github.com/user-attachments/assets/05a8bff3-2cc9-4827-bf12-8526a69406c6" />
<p>Observamos que se crearon correctamente y estan activos.</p>
<h3>2.3. Crear LVM sobre cada RAID</h3>
<p>Para crear los volumenes necesitamos tener instalado lvm2, entonces procedemos con el comando 'sudo apt install lvm2'.</p>
<img width="1265" height="991" alt="image" src="https://github.com/user-attachments/assets/6e753fc8-4487-4707-aa67-1f71980df257" />
<p>Ahora si podemos continuar creando los volumenes fisicos con el comando 'sudo pvcreate /dev/md*' para cada RAID.</p>
<img width="507" height="131" alt="image" src="https://github.com/user-attachments/assets/145cb5ce-4ded-41b1-be3f-33717c2b0be4" />
<p>Luego creamos los grupos para los volumenes, con el comando 'sudo vgcreate vg_name /dev/md*' el name hace referencia a apache, mysql y nginx.</p>
<img width="551" height="132" alt="image" src="https://github.com/user-attachments/assets/7492a46b-7ce0-4454-bfa8-70f8fcb172fc" />
<p>Con esto ya podemos crear los volumenes logicos para cada contenedor, usamos el comando 'sudo lvcreate -n lv_name -l 100%FREE vg_name'.</p>
<img width="705" height="130" alt="image" src="https://github.com/user-attachments/assets/6cbdcf7b-8481-4e61-8304-83dbced2f8d5" />
<h3>2.4. Formatear los volumenes.</h3>
<p>Formateamos los volumenes usando el comando 'sudo mkfs.ext4 /dev/vg_name/lv_name'.</p>
<img width="645" height="767" alt="image" src="https://github.com/user-attachments/assets/f632f37d-b6e5-4db1-9b4b-6580897e2195" />
<h3>2.5. Montar los volumenes.</h3>
<p>Ahora montamos los volumenes en sus respectivos directorios, los cuales crearemos usando el comando 'sudo mkdir /mnt/name' posteriormente montaremos los volumenes con el comando 'sudo mount /dev/vg_name/lv_name /mnt/name'.</p>
<img width="445" height="64" alt="image" src="https://github.com/user-attachments/assets/c93e3e1a-3247-4fb5-b27c-1256fe82d6b8" />
<img width="700" height="67" alt="image" src="https://github.com/user-attachments/assets/5128ae14-c46f-40d5-8677-776c3a6e7e93" />

<h2>3. Configurar los permisos correctos en los directorios de los volumenes.</h2>
<p>Le asignamos los permisos correctos a los directorios para evitar errores en los contenedores, usando el comando 'sudo chown -R www-data:www-data /mnt/name' en el caso de apache y nginx usamos www-data, pero para mysql como no esta instalado y no se instalara en la maquina virtual sino que estara en un contenedor para darle los permisos usaremos root.</p>
<img width="656" height="66" alt="image" src="https://github.com/user-attachments/assets/c166474d-98c5-4d6d-930e-6f4fa37f1e20" />

<h2>4. Crear los directorios de trabajo del proyecto.</h2>
<p>Se crean 3 directorios para cada contenedor con el comando 'mkdir -p ~/proyecto/name'.</p>
<img width="488" height="66" alt="image" src="https://github.com/user-attachments/assets/839dff44-744b-4f50-b4ca-f0d03e9b6c52" />

<h2>5. </h2>
