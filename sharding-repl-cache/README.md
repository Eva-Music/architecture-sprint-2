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

# Задание 3


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

## Подключаемся к первому шарду:

docker exec -it shard1 mongo --port 27018

>rs.initiate(
 {_id: "rs1", members: 
     [
    {_id: 0, host: "shard1:27018"},
    {_id: 1, host: "shard1-replica1:27019"},
    {_id: 2, host: "shard1-replica2:27020"}
]}) 
> exit

## Подключаемся ко второму шарду:

docker exec -it shard2 mongo --port 27021
>rs.initiate(
 {_id: "rs2", members: 
     [
    {_id: 0, host: "shard2:27021"},
    {_id: 1, host: "shard2-replica1:27022"},
    {_id: 2, host: "shard2-replica2:27023"}
]}) 
> exit


## Инцициализируйте роутер и наполните его тестовыми данными:

docker exec -it mongos_router mongo --port 27024

> sh.addShard( "rs1/shard1:27018");
> sh.addShard( "rs2/shard2:27021");

> sh.enableSharding("somedb");

> sh.shardCollection("somedb.helloDoc", { "name" : "hashed" } );

> use somedb

> for(var i = 0; i < 1000; i++) db.helloDoc.insert({age:i, name:"ly"+i});

> db.helloDoc.countDocuments({ age: { $exists:true} })
> exit


