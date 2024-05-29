# Stack DFnoPonto em Docker

## Estrutura
--> No diretório raiz C:\Docker :
-- backend-dfnoponto/
---- app/ : volume onde se encontra o executável do webservice da solução;
---- log/ : volume destinado para depósito do log de execução do webservice;
-- fontend-dfnoponto/
---- conf/ : volume contendo a configuração do proxy reverso Nginx;
---- web/ : volume contendo os arquivos web do frontend da solução;
-- postgis-dfnoponto/ : volume que retém o diretório de persistência do banco de dados;
-- docker-compose.yml : descrição da stack com docker-compose.

## Criação de volumes
--> Os volumes que permitiram aos containers acesso aos arquivos da solução podem ser criados com :
'''sh



    docker volume create --name vol_postgis_dfnoponto --opt type=none --opt device=/home/wubuntu/docker/postgis-dfnoponto --opt o=bind
    docker volume create --name vol_nginx_conf_dfnoponto --opt type=none --opt device=/home/wubuntu/docker/frontend-dfnoponto/conf --opt o=bind
    docker volume create --name vol_nginx_web_dfnoponto --opt type=none --opt device=/home/wubuntu/docker/frontend-dfnoponto/web --opt o=bind
    docker volume create --name vol_service_app_dfnoponto --opt type=none --opt device=/home/wubuntu/docker/backend-dfnoponto/app --opt o=bind
    docker volume create --name vol_service_log_dfnoponto --opt type=none --opt device=/home/wubuntu/docker/backend-dfnoponto/log --opt o=bind
    
    
    mkdir -p /home/wubuntu/docker/frontend-dfnoponto/conf

    mkdir -p /home/wubuntu/docker/frontend-dfnoponto/web



'''

## Inicialização da stack :
--> Inicialização pode ser feito com o comando :
'''sh
	docker-compose -p dfnoponto -f C:\dfnoponto\docker-compose.yml up -d
'''

## Controle da stack :
--> Controle da stack pode ser feito com os comandos :
'''sh
	# Interromper containers
	docker-compose -p dfnoponto -f C:\Docker\docker-compose.yml stop
	# Executar containers interrompidos
	docker-compose -p dfnoponto -f C:\Docker\docker-compose.yml start
	# Remover containers
	docker-compose -p dfnoponto -f C:\Docker\docker-compose.yml rm
'''

## Execução de containers de maneira isolada 
--> 
'''sh
	# Execução do banco de dados
	docker run --name dfnoponto-postgis -p 5432:5432 -v vol_postgis_dfnoponto:/var/lib/postgresql/data -e POSTGRES_PASSWORD=postgres -d mdillon/postgis

	# Execução do proxy reverso nginx
	docker run --name dfnoponto-nginx -p 8888:80 -v vol_nginx_web_dfnoponto:/usr/share/nginx/html -v vol_nginx_conf_dfnoponto:/etc/nginx/conf.d -d nginx:1.16.0-alpine

	# Execução do webservice da aplicação
	docker run --name dfnoponto-app -p 8080:8080 -v vol_service_app_dfnoponto:/opt/app -v vol_service_log_dfnoponto:/opt/logs -e JVM_ARGS="-Xms512m -Xmx4g -Dspring.profiles.active=dev" -d balangandio/jdk8-zoned-entrypoint
'''
