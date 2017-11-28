
# Kurulum Adımları
  - İşletim sistemine göre ;
     - Elasticsearch 6.0.0
     - Kibana 6.0.0
     - Logstash 6.0.0  kurmalısınız. 

Kurulum'u anlatan link aşağıda verilmiştir.Paketler Os'a göre farklılık gösterecektir. Örneğin;Centos kullanılıyorsa 'yum repo' Ubuntu gibi dağıtım kullanılıyorsa 'apt repo' ya göre kurulum yapmanız gerekmektedir.

# Yan Araçların Kurumlum Adımları
  Öncelikle elastic-curator ürününden bahsetmek gerekirse;Curator aracı elasticsearch indexlerinizin cli(command line interface) kullanarak yönetilmesi için python ile geliştirilmesi open source bir kütüphanedir. Bunun için ;

  - pip 9.0.1 
  - curator 5.4.0 (Elasticsearch 6.x)
  
kurmanız gerekmektedir.

# Backup & Restore işlemleri

Tüm kurulum adımları bittikten sonra bu repoda bulunan example klasörü altından "curator" ürününün nasıl kullanıldığı ile ilgili bilgi bilgi edinebilirsiniz.

```sh
$ curator --config curator.yml action_file.yml
```
Snaphot almak için
```sh
$ curator --config curator.yml snapshot.yml
```
Silmek için 
```sh
$ curator --config curator.yml delete_incides.yml
```

gibi komut satırından kullanımı gayet basittir.Yalnız bu komutların ELK(elasticsearch-logstash-kibana) stack'inin kurulduğu makina da otomatik olarak çalışabilmesi için "cron" joblarından yaralanabiliriz.Bu arada "curator_cron" dosyasında linux'ta cron jobların nasıl tanımlandığı ile ilgili bilgi edinebilirsiniz.

# Cron Kullanım Adımları

Cron Yaratmak için
```sh
$ crontab -e 
```

Cron Listelemek için
```sh
$ crontab -l
```

Cron Silmek için
```sh
$ crontab -r
```

### Pluginler
Elasticsearch'te snaphotlar depolamak istediğiniz yerlere göre farklılık gösteririr.
     -  Disk üzerine (fs)
     -  Hadoop (Hdfs)
     -  Cloud 

Örnek olarak aws s3. Biz burada AWS S3 üzerine snapshotlarımız kaydediyor olacağız. Aws S3'e backup almak için öncelikle şu adımları incelemeniz gerekli;

1.S3 plugin'ini kurmalısınız

```sh
$ sudo bin/elasticsearch-plugin install repository-s3
$ bin/elasticsearch-keystore add s3.client.default.access_key
$ bin/elasticsearch-keystore add s3.client.default.secret_key
```

2.Tipi s3 olan bir snaphot oluşturmalısınız

```sh
PUT /_snapshot/{snapshot_name}{
    "type": "s3",
    "settings": {
        "bucket": "{bucket_name}",
        "region": "{region}",
        "base_path": "elasticsearch/backups",
        "max_retries": 3
    }
}
```


Yukarda belirmiş olduğumuz cron joblar zamanlamaları geldiğinde oluşan bu repoya snaphotlarınızı basacaktır.Bu sayede Disk üzerinde eski indexleriniz gereksiz yere depolamaktan kurtulacaksınız ve scale edilebilen bir yere koymuş olacaksınız.Bu da size big data yani sürekli büyüyen datayı yönetebilmeniz için kolaylık sağlıyacaktır.

Tabi yeri geldiğinde bu backupları tekrar restore etmek isteyebilirsiniz.

```sh
$ curator --config curator.yml delete_incides.yml
```

Hangi snapshot'ın içinde hangi index ve kaç adet index var gibi merak ettiğiniz bilgiler olacaktır.

```sh
GET http://localhost:9200/_snapshot/{snapshot_name}/_all?pretty 
```

**Referanslar**
1. https://github.com/elastic/curator
2. https://github.com/minsuk-heo/BigData/tree/master/ch07
3. https://www.youtube.com/watch?v=FhdcT6BQrKI&t=15s
4. https://www.howtoforge.com/tutorial/how-to-install-elastic-stack-on-centos-7/
5. https://crontab.guru/
