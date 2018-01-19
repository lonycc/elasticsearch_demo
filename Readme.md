## ��������

> ES��һ������Lucene������Restful API ��׼�ĸ߿���չ�Ժ͸߿����Ե�ʵʱ���ݷ�����ȫ�ļ�������.

- Node(�ڵ�)��������װ��ES�����ṩ����ת�ƺ���չ�ķ�������

- Cluster(��Ⱥ)��һ����Ⱥ������һ������Node��֯��һ�𣬹��������������ݾ��и��ؾ��⹦�ܡ���Ⱥ������Ψһ��ʶ��Nodeֻ��ͨ��ָ����Ⱥ�������뼯Ⱥ��
 
- Document(�ĵ�)��һ���ĵ���һ�����Ա������Ļ�����Ϣ��λ
 
- Index(����)�������Ǿ��������������ĵ��ļ���
 
- Type(����)��һ��������, ���Զ���һ�ֻ��������

- Field(��)���൱���ĵ���һ���ֶ�

- Shards(��Ƭ)��һ���������Դ洢�����������Ӳ�����ƵĴ������ݡ����磬һ������10���ĵ�������ռ��1TB�Ĵ��̿ռ䣬����һ�ڵ㶼û��������Ĵ��̿ռ䣻���ߵ����ڵ㴦������������Ӧ̫�������ElasticSearch�������ֳ����ɷݣ�ÿ�����־���һ��shard �����㴴��һ��������ʱ�������ָ������Ҫ�ķ�Ƭ��������ÿ����Ƭ����Ҳ��һ���������Ʋ��Ҷ����ġ�����������������������Ա����õ���Ⱥ�е��κνڵ��ϡ� ��Ƭ�ô����ǿ��Զ����ݽ���ˮƽ�ָ��չ������������߲�ѯ���ܺ�������

- Replicas(����)��Replicas��������һ�ݻ��߶�ݿ��� �ṩ�߿���

<br/>

**��Ƭ�͸��Ƶ���������������������ʱ��ָ��������������֮����������κ�ʱ��̬�ظı临�����������ǲ��ܸı��Ƭ��������**

<br/>

**ES���ϵ�����ݿ����**

```
���ݿ�  ---   Index
��      ---   Type
������  ---   Document
������  ---   Field
```

## ����mapping

> һ��mapping��һ������analyzer��ɣ� һ��analyzer����һ������filter��ɵġ���ES�����ĵ���ʱ�������ֶ��е����ݴ��ݸ���Ӧ��analyzer��analyzer�ٴ��ݸ����Ե�filter��

> filter�Ĺ��ܺ�������⣺һ��filter����һ��ת�����ݵķ����� ����һ���ַ������������������һ���ַ���������һ�����ַ���תΪСд�ķ�������һ��filter�ܺõ����ӡ�

> һ��analyzer��һ��˳�����е�filter��ɣ�ִ�з����Ĺ��̾��ǰ�˳��һ��filterһ��filter���ε��ã� ES�洢���������õ��Ľ����

> �ܽ���˵�� mapping�����þ���ִ��һϵ�е�ָ����������ת�ɿ������������

## Ĭ��analyzer

> Ĭ�ϵ�analyzer�Ǳ�׼analyzer, �����׼analyzer������filter��`token filter`, `lowercase filter`��`stop token filter`��

> ��ȫ����������Ҫ���ĵ������������������ĵ�����ȡ��Ԫ��Token�����㷨��Ϊ�ִ�����Tokenizer�����ڷִ�ǰԤ������㷨��Ϊ�ַ���������Character Filter������һ�������Ԫ���㷨��Ϊ��Ԫ��������Token Filter�������õ��ʣ�Term���������������㷨��Ϊ��������Analyzer����

> �ĵ������ʵ�������Ϊ��Ƶ��Frequency������������Ὠ�������ĵ�����������Ϊ����������Inverted Index����

> analyzer��˳���������£�1. ʹ��`CharacterFilter`�����ַ�; 2. ʹ��`Tokenizer`�ִ�; 3. ʹ��`TokenFilter`���˴�.

## Mapping

> һ��������`Mapping`�����·ֳ�`settings`��`mappings`��������: `settings`��Ҫ������`index`��һЩ���������Ϣ�����Ƭ��`number_of_shards`��������`number_of_replicas`��`tranlog`ͬ��������`refresh`�����ȡ�`mappings`������Ҫ�������ṹ˵����`mappings`�ֳַ�`_all`��`_source`��`properties`�����֡�

### index_options

�ò����������ӵ�������������Ϣ, Ϊ�������͸���.
 
`docs`: ֻ�����ĵ���, freqs:�ĵ��źʹ�Ƶ���ᱻ�洢

`positions`: �ĵ���/����/�ʵ�λ�ûᱻ����

`offsets`: �ĵ���/����/�ʵ�λ��/�ַ�ƫ�ƶ��ᱻ����

�����ַ���Ĭ�ϻ�ʹ��positions, ����Ĭ��docs

### include_in_all

field������_all��, Ĭ��Ϊtrue.


### term_vector

`no|yes|with_positions|with_offsets|with_positions_offsets`

�������Ҫ������, �Ǿ�ѡ��with_positions_offsets, ����ѡ��no


### _source��

_source���������ʱԭʼ��json�ĵ�, _source�򲻱�����, �����洢.ִ��get/search����ᱻ����(�����ֻ��ָ�����ֶ�,��ô���Խ���_source��,����Ҫ), ����㲻ʹ��_source ��, ��ͱ�������_stored��Ҫ�洢����


### _all��

ӵ��������, ʹ�ÿո���Ϊ�ָ����������ֶ�ƴ����һ��Ĵ��ַ���. ���������ڷ���������, �����洢.

**����demo**

```
PUT /demo
{
    "settings:" {
        "number_of_shards": 5,
        "number_of_replicas": 1,
        "index.tranlog.flush_threshold_ops": "100000",
        "index.refresh_interval": "-1"
    },
    "mappings": {
        "demo": {  //��������
            "dynamic": true,
            "_all": {  //all Field
                "analyzer": "like",
                "enabled": false
            },

            "_source": {  //sorce Filed, �����Ϊ�����ļ������һ��Դ����
                "enabled": true,
                "compress": true,
                "includes": ["title", "desc"],  //����
                "excludes" ["origin"],   //������
            }

            "properties": {
                "id": {
                    "type": "text|keyword|date",
                    "index": false,   //���ֶβ�����
                    "analyzer": "whitespace",   //���õķ�����
                    "format": "",
                    "store": true|false,  //��洢������������ȥ
                    "include_in_all": true|false,  //�Ƿ������_all field, Ĭ��Ϊtrue, ����index����Ϊfalse
                },
                "manager": {  //���filed�Ǹ�object
                    "properties": {
                        "age":  { "type": "integer" },
                        "name": { "type": "text"  }
                    }
                },
                "employees": { 
                    "type": "nested",  //nested����objest���Ϳ��԰�����filed
                    "properties": {
                        "age":  { "type": "integer" },
                        "name": { "type": "text"  }
                    }
                },

            }
        }
    }
}
```

**ָ��_id�ֶδ�������**
```
curl -XPUT 172.23.130.202:9200/my-index/my-type/url -d '
{
    "mappings": {
        "my-type": {
            "_id": {"path": "url"},
            "properties": {
                "url": {
                    "type": "keyword",
                }
            }
        }
    }
}'
```

**MongoDBͬ����ES**

`pip install mongo-connector`

`pip install elastic2_doc_manager`

`mongo-connector --auto-commit-interval=0 -m mongo:27017 -t elasticsearch:9200 -d elastic2_doc_manager -n db.tb_1,db.tb_2`

**ES���ýӿ�**

`GET /_cat` # �������_cat�ӿ�

`GET /_cluster/health?pretty=true` # _cluster�ӿ�

`GET /_cluster/stats?pretty=true`  # ��ʾ��Ⱥϵͳ��Ϣ

`GET /_cluster/state?pretty=true`  # ��ʾ��Ⱥ��ϸ��Ϣ

`GET /_cluster/pending_tasks?pretty=true`  # ��ȡ��Ⱥ�ѻ�����

`POST /_cluster/nodes/_shutdown` # �رռ�Ⱥ

`POST /_cluster/nodes/_all/_shutdown`

`POST /_shutdown?delay=10s`

`GET /_cat/health?v` # ��鼯Ⱥ����

`GET /_cat/nodes?v` # ��ȡ��Ⱥ�нڵ��б�

`GET /_cat/indices?v` # �г���������

`PUT /demo`    # ��������

`DELETE /demo` # ɾ������
