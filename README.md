# Zabbix-Apache

### Passo 1: Adicionar o parâmetro no final do arquivo httpd.conf.

    #vim /etc/httpd/conf/httpd.conf

    <Location /server-status>

          SetHandler server-status

          Order deny,allow

          Deny from all

          Allow from 127.0.0.1
          
    </Location>

    # Keep track of extended status information for each request
    
    ExtendedStatus On


### Passo 2: Reiniciar o apache

    #service httpd restart

### Passo 3: Testar configuração

    #wget --quiet -O - http://127.0.0.1/server-status?auto


#### Resultado esperado, algo parecido com:

    Total Accesses: 501

    Total kBytes: 383

    Uptime: 3203

    ReqPerSec: .156416

    BytesPerSec: 122.445

    BytesPerReq: 782.818

    BusyWorkers: 1

    IdleWorkers: 4

    Scoreboard: __W__.........................

### Passo 4: Adicionar o parâmetro Include=/etc/zabbix/UserParameters/ no zabbix_agentd.conf.

    #vim /etc/zabbix/zabbix_agentd.conf

### Passo 5: Criar a pasta UserParameters dentro de /etc/zabbix.

    #mkdir /etc/zabbix/UserParameters

### Passo 6: Criar o arquivo apache.status.conf em /etc/zabbix/UserParameters.

     #touch /etc/zabbix/UserParameters/apache.status.conf

### Passo 7: Adicionar o conteúdo abaixo no arquivo apache.status.conf

     #vim /etc/zabbix/UserParameters/apache.status.conf

    UserParameter=apache.totalaccesses,wget --quiet -O - http://127.0.0.1/server-status?auto | grep "Total Accesses:" | awk '{print$3}'
    UserParameter=apache.totalkbytes,wget --quiet -O - http://127.0.0.1/server-status?auto | grep "Total kBytes:" | awk '{print$3}'
    UserParameter=apache.uptime,wget --quiet -O - http://127.0.0.1/server-status?auto | grep "Uptime:" | awk '{print$2}'
    UserParameter=apache.reqpersec,wget --quiet -O - http://127.0.0.1/server-status?auto | grep "ReqPerSec:" | awk '{print$2}'
    UserParameter=apache.bytespersec,wget --quiet -O - http://127.0.0.1/server-status?auto | grep "BytesPerSec:" | awk '{print$2}'
    UserParameter=apache.bytesperreq,wget --quiet -O - http://127.0.0.1/server-status?auto | grep "BytesPerReq:" | awk '{print$2}'
    UserParameter=apache.busyworkers,wget --quiet -O - http://127.0.0.1/server-status?auto | grep "BusyWorkers:" | awk '{print$2}'
    UserParameter=apache.idleworkers,wget --quiet -O - http://127.0.0.1/server-status?auto | grep "IdleWorkers:" | awk '{print$2}'
    UserParameter=apache.cpuload,wget --quiet -O - http://127.0.0.1/server-status?auto | grep "CPULoad:" | awk '{print$2}'
    UserParameter=apache.waitforconnection,wget --quiet -O - http://127.0.0.1/server-status?auto | grep "Scoreboard:" | awk '{print$2}' | awk 'BEGIN { FS = "_" } ; { print NF-1 }'
    UserParameter=apache.startingup,wget --quiet -O - http://127.0.0.1/server-status?auto | grep "Scoreboard:" | awk '{print$2}' | awk 'BEGIN { FS = "S" } ; { print NF-1 }'
    UserParameter=apache.readingrequest,wget --quiet -O - http://127.0.0.1/server-status?auto | grep "Scoreboard:" | awk '{print$2}' | awk 'BEGIN { FS = "R" } ; { print NF-1 }'
    UserParameter=apache.sendingreply,wget --quiet -O - http://127.0.0.1/server-status?auto | grep "Scoreboard:" | awk '{print$2}' | awk 'BEGIN { FS = "W" } ; { print NF-1 }'
    UserParameter=apache.keepalive,wget --quiet -O - http://127.0.0.1/server-status?auto | grep "Scoreboard:" | awk '{print$2}' | awk 'BEGIN { FS = "K" } ; { print NF-1 }'
    UserParameter=apache.dnslookup,wget --quiet -O - http://127.0.0.1/server-status?auto | grep "Scoreboard:" | awk '{print$2}' | awk 'BEGIN { FS = "D" } ; { print NF-1 }'
    UserParameter=apache.closeconnection,wget --quiet -O - http://127.0.0.1/server-status?auto | grep "Scoreboard:" | awk '{print$2}' | awk 'BEGIN { FS = "C" } ; { print NF-1 }'
    UserParameter=apache.logging,wget --quiet -O - http://127.0.0.1/server-status?auto | grep "Scoreboard:" | awk '{print$2}' | awk 'BEGIN { FS = "L" } ; { print NF-1 }'
    UserParameter=apache.gracefullyfinish,wget --quiet -O - http://127.0.0.1/server-status?auto | grep "Scoreboard:" | awk '{print$2}' | awk 'BEGIN { FS = "G" } ; { print NF-1 }'
    UserParameter=apache.idlecleanup,wget --quiet -O - http://127.0.0.1/server-status?auto | grep "Scoreboard:" | awk '{print$2}' | awk 'BEGIN { FS = "I" } ; { print NF-1 }'
    UserParameter=apache.versao,apachectl -v | awk '/^Server version:/ {print $3}'

### Passo 8: Altere a permissão do arquivo apache.status.conf

    #cd /etc/zabbix/UserParameters

    #chown zabbix:zabbix apache.status.conf

### Passo 9: Reinicie o zabbix-agent

    #service zabbix-agent restart

### Passo 10: Vincule o template Template Apache Status no host alterado.

# Telegram @iakim
