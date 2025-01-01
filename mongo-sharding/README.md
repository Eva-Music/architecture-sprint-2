## Задание 1

https://drive.google.com/file/d/16znAyUZrUD-cztCRuQVb5nWGQz5rxpvg/view?usp=sharing


# pymongo-api

## Как запустить

Запускаем mongodb и приложение

```shell
docker compose up -d
```

## Доступные эндпоинты

Список доступных эндпоинтов, swagger http://<ip виртуальной машины>:8080/docs

# Задание 2


## Подключитесь к серверу конфигурации и сделайте инициализацию:

docker exec -it configSrv mongo --port 27017

>rs.initiate(
  {
    _id : "config_server",
       configsvr: true,
    members: [
      { _id : 0, host : "configSrv:27017" },
    ]
  }
);
> exit


## Инициализируйте шарды:

docker exec -it shard1 mongo --port 27018

> rs.initiate(
    {
      _id : "shard1",
      members: [
        { _id : 0, host : "shard1:27018" },
      ]
    }
);
> exit

docker exec -it shard2 mongo --port 27019

> rs.initiate(
    {
      _id : "shard2",
      members: [
        { _id : 0, host : "shard2:27019" }
      ]
    }
  );
> exit


## Инцициализируйте роутер и наполните его тестовыми данными:

docker exec -it mongos_router mongo --port 27020

> sh.addShard( "shard1/shard1:27018");
> sh.addShard( "shard2/shard2:27019");

> sh.enableSharding("somedb");

> sh.shardCollection("somedb.helloDoc", { "name" : "hashed" } );

> use somedb

> for(var i = 0; i < 1000; i++) db.helloDoc.insert({age:i, name:"ly"+i});

> db.helloDoc.countDocuments({ age: { $exists:true} })
> exit