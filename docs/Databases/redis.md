# Redis

## Setup

### Docker

```shell
# pull the image from docker hub
$ docker run --name redis-learn -p 6370:6370 -d redis 

# connect to container and redis shell
$ docker exec -it redis-learn redis-cli
```

### To benchmark

```
docker exec -it redis-learn redis-benchmark -n 1000 -d 10000
# -d for bytes of data

redis-benchmark -n 1000 -d 10000
```

### To set max memory limit

```
> config set maxmemory 128M
```

### Set & Get value

* set key value
* get key value
* check whether key exists, returns integer
  * 0 - false | 1 - true

```shell
> set name "uday"
OK

> get name
"uday"

> exists name
(integer) 1
```

### **Key related ops**

* Get all keys
* Delete all keys ( sync | async )

```shell
> keys *
1) "name"

> flushall 
# options : async|sync
OK
```

### Set key with expiry time

```shell
# After 5 seconds, this  key will be deleted
> set key "value" EX [expiry time in seconds]
> get key
> exists key

# expire after X seconds
> set key "value"
> get key
> expire key X

# to check time remaining 
> ttl key

# Another way, to expire after 10 seconds
> setex key 10 "value"
```

### Delete Key

```shell
> del name
(integer) 1
```

### Set & Get multiple values

```shell
> mset first "uday" last "yadav"

> mget first last
1) "uday"
2) "yadav"
```

### Miscellaneous

```shell
> set name "uday yadav"

# Specifies the range : from 0th char to 3rd char
> getrange name 0 3
"uda"
> strlen name
10

# Append to key
> set name "uday"
> append name " yadav"
> get name
"uday yadav"
```

### Maths operations

```shell
> set count 1

> incr count
(integer) 2

> incrby count 10
(integer) 12

> decr count 
(integer) 11

> decrby count 2
(integer) 9

> set pi 3.14
> incrbyfloat pi 0.1
"3.24"
```

### Lists in Redis

```shell
# lpush adds value infront
> lpush country india
> lpush country USA
> lpush country UK

# get the element at index, start from 0
> lindex country 2

# to add values to the bottom
> rpush country Australia

# to get all values in list
> lrange country 0 -1
1) "UK"
2) "USA"
3) "india"
4) "Australia"

# to get first 2 values
> lrange country 0 1

# get list length
> llen country

# use lpop and rpop to remove the data from top and bottom
# and it returns the element
> lpop country
> rpop country

# to change a key's value
> lset country 0 germany

# to insert values before and after 
> linsert country before "germany" "new zealand"
> linsert country after "germany" "UAE"

# use lpushx and rpushx to add key to list only if it exists
# else returns 0
```

### Sorting List

```shell
# Alpha is needed only for strings, nothing for integers
> sort country ALPHA
> sort country desc ALPHA
```

### Sets in Redis

```shell
> sadd tech golang
(integer) 1

# when you do it again
> sadd tech golang
(integer) 0

> sadd tech postgis python aws
> sadd tech1 aws python mysql nodejs

# to see all members
> smembers tech

# to get the length of set
> scard tech

# to search the set
> sismember tech aws
1

# to get the diff between to sets
> sdiff tech tech1

# to store the difference btw 2 sets to a new set 
> sdiffstore tech tech1 [more set]

# to check intersection
> sinter tech tech1
```

### Sorted Set Redis

```shell
# add key values
> zadd users 10 uday 
> zadd users 5 uday1 8 uday2

# to get all users
> zrange users 0 -1
> zrange users 0 -1 withscores
# in reverse order
> zrevrange users 0 -1

# to get the length of the string
> zcard users
3

# to get key's value over a range
> zcount users 0 2

# to remove member
> zrem users uday
```

### Hashes in Redis

```shell
# add keys to a set
> hset myhash name uday
> hset myhash email dev117uday@gmail.com

# get all keys from hashset
> hkeys myhash

# to get all values
> hvals myhash

# get value
> hget myhash name

# to check for keys 
> hexists myhash name

# check the length    
> hlen myhash

# to set multiple values at once
> hmset myhash country india phone_no 9810039759 age 24

# to get multiple values
> hmget myhash country name email

# increment some value
> hincrby myhash age 2

# to delete key from set
> hdel myhash age

# to avoid over writting the values
> hsetnx myhash name Uday
```

### Transaction

```shell
# to go in transaction mode
> multi
(TX)> set key1 uday
(TX)> set key2 yadav
(TX)> set key3 dev117uday
(TX)> exec
1) OK
2) OK
3) OK

# to discard transaction
discard
```

**Pub/Sub**

```shell
# To listen to a channel
> subscribe my-chat

# To publish to a channel
> publish my-chat "hello world"

# to subscribe to channel with patterns in name
> psubscribe chats*
> psubscribe h?llo
```

* If no one is sub to the channel you specify in publish, it returns 0

### GeoSpatial Data

```shell
# add geo spatial data in long : lat
> GEOADD maps 77.216721 28.644800 delhi
> GEOADD maps 72.877426 19.076090 mumbai

# data is tored in sorted set data structure
> zrange maps 0 -1
1) "mumbai"
2) "delhi"

# to get the geohash of city
> GEOHASH maps delhi
1) "ttnfvnes010"

# to get long:lat
> GEOPOS maps delhi
1) 1) "77.21672326326370239"
   2) "28.64479890853065314"

# to get distance, in meter (default)
> GEODIST maps delhi mumbai
"1151873.1929"

> GEODIST maps delhi mumbai km
"1151.8732"

# within distance

> GEORADIUS maps 77.216721 28.644800 2000 km
1) "delhi"
2) "mumbai

> GEORADIUS maps 77.216721 28.644800 2000 km withcoord
1) 1) "delhi"
   2) 1) "77.21672326326370239"
      2) "28.64479890853065314"
2) 1) "mumbai"
   2) 1) "72.87742406129837036"
      2) "19.07608965708350723"
      
> GEORADIUS maps 77.216721 28.644800 2000 km withcoord withdist
1) 1) "delhi"
   2) "0.0003"
   3) 1) "77.21672326326370239"
      2) "28.64479890853065314"
2) 1) "mumbai"
   2) "1151.8732"
   3) 1) "72.87742406129837036"
      2) "19.07608965708350723"

> GEORADIUSBYMEMBER maps delhi 1300 km
1) "delhi"
2) "mumbai"

> GEORADIUSBYMEMBER maps delhi 1300 km withcoord withdist desc|asc
1) 1) "mumbai"
   2) "1151.8732"
   3) 1) "72.87742406129837036"
      2) "19.07608965708350723"
2) 1) "delhi"
   2) "0.0000"
   3) 1) "77.21672326326370239"
      2) "28.64479890853065314"
```
