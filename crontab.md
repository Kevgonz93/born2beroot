# CRONTAB

## ¿Qué es?

Es un administrador de procesos en segundo plano, permite PROGRAMAR la ejecución automática de tares/comandos(cron jobs) en intervalos indicados. ESto es útil para realizar operaciones de mantenimiento como hacer copias de respaldos, enviar emails o ejecutar scripts.

-minutos- -hora- -día de mes- -mes- -días de la semana-         -comando-
    *       *         *         *           *               sh /ruta/comando.sh

* = todo/as

- sudo crontab -u root -e

Comando que gestiona el archivo de crontab. especificando el usuario, puedes editar, lista o eliminar el crontab de ese usuario.

- */10 * * * * sh /ruta/monitoring.sh

Cada 10 minutos ejecturará el comando monitoring.hs

