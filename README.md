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

<h2>5. Crear los Dockerfile personalizados</h2>
<p>Ahora creamos los Dockerfile personalizados que usaremos, usamos el comando 'nano ~/proyecto/name/Dockerfile'.</p>
<p>Dockerfile de apache:</p>
<img width="415" height="271" alt="image" src="https://github.com/user-attachments/assets/8dd3d377-16e4-4740-92d6-b40a01569e2f" />
<p>Dockerfile de mysql:</p>
<img width="302" height="186" alt="image" src="https://github.com/user-attachments/assets/72161d47-7adc-4ac8-ae1d-f8df74a77bd7" />
<p>Dockerfile de nginx:</p>
<img width="488" height="161" alt="image" src="https://github.com/user-attachments/assets/3054491c-c05a-4bd2-a101-3cdfff8c46dd" />
<p>default.conf de nginx:</p>
<img width="374" height="203" alt="image" src="https://github.com/user-attachments/assets/a1d113ad-6566-4dd8-91d8-9569314bd023" />

<h2>6. Construir las imágenes Docker</h2>
<p>Primero hay que instalar docker en la maquina virtual.</p>
<p>sudo apt install ca-certificates curl gnupg lsb-release</p>
<img width="1017" height="613" alt="image" src="https://github.com/user-attachments/assets/1aa644d8-3b4d-434a-a59e-4d0456e604f4" />
<p>sudo mkdir -p /etc/apt/keyrings</p>
<p>curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg</p>
<p>echo "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null</p>
<img width="1849" height="87" alt="image" src="https://github.com/user-attachments/assets/638b3c6c-e04f-40db-985d-bd02339dad14" />
<p>sudo apt update</p>
<img width="848" height="260" alt="image" src="https://github.com/user-attachments/assets/29286d2c-9b66-4e9a-8930-bcd934900b88" />
<p>sudo apt install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin</p>
<img width="1624" height="993" alt="image" src="https://github.com/user-attachments/assets/6b486c76-4651-46ad-942b-57ab70b2abae" />
<p>Ahora se ejecutan unos comandos adicionales para iniciar y habilitar los servicios de docker.</p>
<p>sudo systemctl start docker</p>
<p>sudo systemctl enable docker</p>
<img width="1072" height="84" alt="image" src="https://github.com/user-attachments/assets/1c7f6977-73e7-4802-a35e-4de431bea86f" />
<p>Ejecutamos 'sudo docker run hello-world' para verificar que este funcionando correctamente.</p>
<img width="803" height="593" alt="image" src="https://github.com/user-attachments/assets/81d5d3c3-fed7-4168-b400-14c886fa3d05" />
<p>Observamos que esta funcionando correctamente.</p>
<p>Ahora se construyen las imagenes docker usando 'docker build'.</p>
<p>Apache:</p>
<img width="1176" height="482" alt="image" src="https://github.com/user-attachments/assets/bd863c5d-4e5a-415a-8d9f-ff4643b5d608" />
<p>Mysql:</p>
<img width="1173" height="725" alt="image" src="https://github.com/user-attachments/assets/2f9c9666-9aec-42ac-bfe5-05ae48b81033" />
<p>Nginx:</p>
<img width="1184" height="791" alt="image" src="https://github.com/user-attachments/assets/2cbf9bc5-fc84-4c8d-ba26-540eedb10527" />
<p>Verificamos que las imagenes existan con 'sudo docker images'.</p>
<img width="719" height="157" alt="image" src="https://github.com/user-attachments/assets/04354f61-55c2-443c-8ffb-ceef91190051" />
<p>Observamos que si existen.</p>

<h2>7. Usar las imágenes con los volumenes LVM</h2>
<p>Ejecutaremos los docker run con las imagenes que se crearon en el punto anterior.</p>
<p>Apache:</p>
<img width="1086" height="39" alt="image" src="https://github.com/user-attachments/assets/3b934f3a-8bdd-47f7-b156-9fe722d62fa9" />
<p>Mysql:</p>
<img width="1368" height="41" alt="image" src="https://github.com/user-attachments/assets/722b0ef0-4fde-498e-88f9-90a93e9dc835" />
<p>Nginx:</p>
<img width="1136" height="43" alt="image" src="https://github.com/user-attachments/assets/476babc5-7f29-4f0c-aa1f-4c95f739d25d" />
<p>Verificamos con el comando 'sudo docker ps' que esten los contenedores.</p>
<img width="1609" height="107" alt="image" src="https://github.com/user-attachments/assets/e7a01e2d-afeb-4ed3-afbf-1280a1a56301" />
<p>Observamos que existen y estan corriendo. </p>

<h2>8. Pruebas de funcionamiento.</h2>
<p>Ahora pasaremos a las pruebas de funcionamiento.</p>
<p>Apache:</p>
<p>Para apache creamos un archivo index.html con un mensaje dentro, reiniciamos el contenedor y luego buscamos la ip localhost en el puerto del apache en el navegador y deberiamos ver el index.html.</p>
<p>echo "<h1>Hola desde Apache</h1>" | sudo tee /mnt/apache/index.html</p>
<img width="899" height="43" alt="image" src="https://github.com/user-attachments/assets/cc335969-a041-4832-9d0c-d14ef96619d1" />
<p>sudo docker restart apache</p>
<img width="494" height="46" alt="image" src="https://github.com/user-attachments/assets/1b3a01de-d2e0-446c-b725-28ad0fbba7e1" />
<p>Abrimos http://localhost:8080:</p>
<img width="1277" height="967" alt="image" src="https://github.com/user-attachments/assets/4f23b855-19f0-47d8-bf5a-b6a90834d917" />
<p>Observamos que funciona el contenedor de apache.</p>
<p>Nginx:</p>
<p>Para nginx hacemos lo mismo que en apache pero cuando vayamos a abrir en el navegador la el puerto de la ip es 8081.</p>
<p>echo "<h1>Nginx funcionando</h1>" | sudo tee /mnt/nginx/index.html</p>
<img width="889" height="46" alt="image" src="https://github.com/user-attachments/assets/052502a5-207b-4d15-b4ee-00acd5c10ffb" />
<p>sudo docker restart nginx</p>
<img width="479" height="43" alt="image" src="https://github.com/user-attachments/assets/e65b2663-16d2-44bf-8be9-729e8950d732" />
<p>Abrimos http://localhost:8081:</p>
<img width="1279" height="973" alt="image" src="https://github.com/user-attachments/assets/29fad77c-24bf-41d8-9a60-0c47c2edb703" />
<p>Observamos que funciona el contenedor de nginx.</p>
<p>Mysql:</p>
<p>Para esta prueba debemos acceder al contenedor.</p>
<p>sudo docker exec -it mysql mysql -u root -p</p>
<img width="797" height="349" alt="image" src="https://github.com/user-attachments/assets/6614b747-c5ee-42ca-a9a0-179a594b980f" />
<p>Ahora ejecutamos 2 comandos para ver las bases de datos que hay creadas y otra para crear una nueva de prueba que nos servira para verificar la persistencia de datos en el siguiente punto.</p>
<p>SHOW DATABASES;</p>
<img width="247" height="251" alt="image" src="https://github.com/user-attachments/assets/356aab91-1593-44c6-9148-82a79e974c07" />
<p>CREATE DATABASE prueba;</p>
<img width="359" height="335" alt="image" src="https://github.com/user-attachments/assets/25c2f705-ad10-415f-97fe-b2b93f64a0ef" />
<p>Observamos que el contenedor de mysql tambien funciona correctamente.</p>

<h2>9. Persistencia de datos.</h2>
<p>Ahora vamos a verificar que la persistencia con los volumenes esten funcionando, para esto vamos a modificar los index.html que se crearon en el punto anterior y a reiniciar los contenederos, estos cambios deberian persistir.</p>
<p></p>
