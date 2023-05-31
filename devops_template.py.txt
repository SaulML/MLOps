import paramiko

# Define SSH connection details
host = 'remote_server'
port = 22
username = 'your_username'
password = 'your_password'

# Define local file paths
local_app_path = 'path/to/local/application'
remote_app_path = '/var/www/application'

try:
    # Connect to the remote server
    ssh = paramiko.SSHClient()
    ssh.set_missing_host_key_policy(paramiko.AutoAddPolicy())
    ssh.connect(host, port, username, password)

    # Upload the application files to the remote server
    sftp = ssh.open_sftp()
    sftp.put(local_app_path, remote_app_path)
    sftp.close()

    # Execute deployment commands on the remote server
    commands = [
        'cd /var/www/application',
        'npm install',
        'npm run build',
        'pm2 restart app'
    ]
    for command in commands:
        stdin, stdout, stderr = ssh.exec_command(command)
        print(stdout.read().decode())
        print(stderr.read().decode())

    # Close the SSH connection
    ssh.close()

    print('Deployment successful!')
except paramiko.AuthenticationException:
    print('Authentication failed. Please check your credentials.')
except paramiko.SSHException as e:
    print('SSH error occurred:', str(e))
except paramiko.SFTPException as e:
    print('SFTP error occurred:', str(e))
except Exception as e:
    print('An error occurred:', str(e))
