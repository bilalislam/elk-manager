
# What is Elastic Curator ?

- Elasticsearch Curator helps you curate, or manage, your Elasticsearch indices and snapshots by:
  - Obtaining the full list of indices (or snapshots) from the cluster, as the actionable list
  - Iterate through a list of user-defined filters to progressively remove indices (or snapshots) from this actionable list as needed.
  - Perform various actions on the items which remain in the actionable list.

Curator is written in Python, so it is well supported almost all operating systems. Installation is a breeze with a 
```$ pip install elasticsearch-curator ```. That provides you with the curator command that you can use. There’s also a Python API that you can access from your Python programs, but we only use the command line interface.

# Installation Steps
  - Operating Systemize ;
     - Elasticsearch 6.0.0
     - Kibana 6.0.0
     - Logstash 6.0.0  kurmalısınız. 

The installation url has been descripted below. Packages will vary by OS. For example, if Centos is used and 'yum repo' is used for distribution like Ubuntu, then you need to install according to 'apt repo'.

# Installation Steps of Side Tools
  First of all, if you need to talk about the elastic-curator product, the Curator tool is an open source library developed with python so that your ElasticSearch indexes can be managed using cli (command line interface). For this ;

  - pip 9.0.1 
  - curator 5.4.0 (Elasticsearch 6.x)
  
you need to install it.

# Backup & Restore Steps

After all the installation steps are finished, you can find out how to use the "curator" product under the example folder in this repo.

```sh
$ curator --config curator.yml action_file.yml
```
To get a snapshot
```sh
$ curator --config curator.yml snapshot.yml
```
To remove a snapshot
```sh
$ curator --config curator.yml delete_incides.yml
```

(ElasticSearch-LogStash-Kibana) stacks can only be run automatically on the machine where it is installed. In the meantime, the "curator_cron" file contains information about how to define cron jobs in linux tha you can learn.

# Cron Usage Steps

To create a cron
```sh
$ crontab -e 
```

To list a cron
```sh
$ crontab -l
```

To remove a cron
```sh
$ crontab -r
```

### Plugins
Snapshots at Elasticsearch will vary depending on where you want to store them.

- Storage Types;
     -  File System (fs)
     -  Hadoop File System(Hdfs)
     -  Cloud 

We will use aws s3 for get a snapshots.First of all you need to follow these steps for it below;

1.You should install the S3 plugin

```sh
$ sudo bin/elasticsearch-plugin install repository-s3
$ bin/elasticsearch-keystore add s3.client.default.access_key
$ bin/elasticsearch-keystore add s3.client.default.secret_key
```

2.You must create a snapshot of type S3

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


The cron jobs that we show up above will print out these repo snapshots when they arrive. On this page you will get rid of the old indexes unnecessary on the disk and you will be put in a place that can be scaled. This will give you the convenience to manage big data.

Of course, you may want to restore these backups once they are available.

```sh
$ curator --config curator.yml restore.yml
```

Which snapshot will have the information you are interested in, such as which index and how many indexes are in it.

```sh
GET http://localhost:9200/_snapshot/{snapshot_name}/_all?pretty 
```

You need to register elastic license page and get basic license jsonfile when It expired to your ELK license .!!!! And run the link for license update.

```sh
curl -XPUT 'http://localhost:9200/_xpack/license?acknowledge=true' -H "Content-Type: application/json" -d @license.json
```

**References**
1. https://github.com/elastic/curator
2. https://github.com/minsuk-heo/BigData/tree/master/ch07
3. https://www.youtube.com/watch?v=FhdcT6BQrKI&t=15s
4. https://www.howtoforge.com/tutorial/how-to-install-elastic-stack-on-centos-7/
5. https://crontab.guru/
