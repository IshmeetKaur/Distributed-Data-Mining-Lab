## Text-mine the relationship of Protein & Cell with LocText with Map Reduce

## Description

This week we finalized the project for finding the locations of proteins and running the jobs in a distributed manner via map reduce . Visualizations would be  done with the help of kibana and elastic search.

### Storing Protein Locations: LocText run.py script

For updating the elastic search records stored in the last weeks(Week 10) , we used Hadoop Map reduce Job to distribute the parsing and updating process . 
The  parsing of the successful unique ids created in (week 9) were accessed and with the help of the loctext repository and the using loctext created in week 10, we wrote mapper and reducer in order to distribute the jobs on the cluster and then update elastic search on the master. 
The scripts for mapper is:

![mapper_run](https://github.com/IshmeetKaur/Distributed-Data-Mining-Lab/blob/master/Week12/mapper_run.png)

The successful unique IDs used in the reducer are:

![successful_ids_reducer](https://github.com/IshmeetKaur/Distributed-Data-Mining-Lab/blob/master/Week12/successful_ids_reducer.png)

A reducer (accessing the all the succesful unique IDs and using the Loctext modified scriped of run.py called editedrun.py) was written .
The script for te reducer is 

```
import sys
import requests
from elasticsearch import Elasticsearch
#import traceback
sys.path.insert(0, '/root/LocText')

from  editedRun import run_with_argv
import json
import requests

elastic_uri = "http://10.155.208.219:9200/"
res = requests.get('http://10.155.208.219:9200')
es = Elasticsearch([{'host': '10.155.208.219', 'port': 9200}])
pubmed_index = "pubmedhadooptest"
def running_LocText(line = ""):
    print(line)
    try:
        #print(line.strip())
        full_uri = elastic_uri+pubmed_index+"/_search?"
        #elastic_result = search(full_uri,"24639918_3955094")
        elastic_result = search(full_uri,line.strip())
        #print(elastic_result)
        text_Loctext = elastic_result["hits"]["hits"][0]["_source"]["rest"]
        #text_Loctext = 'GCN2 was constitutively localized to the nucleolus or recruited to the nucleolus by amino acid starvation stress'
        result = run_with_argv(text = text_Loctext)
        #print(type(result))
        entities = "" # string to store all entities associated with text
        entities_text = ""
        entities_start_offset = ""
        prots = []
        locs = []
        count = 0

        #Making prots
        for entity in result.predicted_entities():
            #entities +="class_id: "+ entity.class_id + " " +str(entity.offset) + " " + entity.text + " " + str(entity.norms)
            #entities += str(entity)+","
            entry_dict = {}
            entry_dict['text'] = entity.text
            entry_dict['start_offset'] = entity.offset
            entry_dict['end_offset'] = entity.offset + len(entity.text)
            if entity.class_id == "e_1":
                entry_dict['uac'] = list(entity.norms.values())
                prots.append(entry_dict)

            else:
                entry_dict['go'] =  list(entity.norms.values())
                locs.append(entry_dict)
        relations = ""
        global prot_loc
        prot_loc = []
        protein_uac = []
        loc_go = []
        for relation in result.predicted_relations():
            prot_loc = []
            protein_uac = []
            loc_go = []
            relations += str(relation)+","
            if relation.entity1.class_id == "e_1":
                prot_ref = str(relation.entity1.offset) + "_" + str(relation.entity1.offset+len(relation.entity1.text))
                protein_uac.append(relation.entity1.norms["n_7"].split(","))
            else:
                loc_ref = str(relation.entity1.offset) + "_" + str(relation.entity1.offset+len(relation.entity1.text))
                loc_go.append(relation.entity1.norms["n_9"].split(","))

            if relation.entity2.class_id == "e_1":
                prot_ref = str(relation.entity2.offset) + "_" + str(relation.entity2.offset+len(relation.entity2.text))
                protein_uac.append(relation.entity2.norms["n_7"].split(","))
            else:
                loc_ref = str(relation.entity2.offset) + "_" + str(relation.entity2.offset+len(relation.entity2.text))
                loc_go.append(relation.entity2.norms["n_9"].split(","))
            for protein in protein_uac[0]:
                for loc_id in loc_go[0]:
                   prot_loc_dict = {}
                   prot_loc_dict["go"] = loc_id
                   prot_loc_dict["uac"] = protein
                   prot_loc_dict["loc_ref"] = loc_ref
                   prot_loc_dict["prot_ref"] = prot_ref
                   prot_loc.append(prot_loc_dict)

        update_elastic_search(prots,line.strip(),locs,prot_loc)
    except Exception as e:
        #traceback.print_exc()
        pass

def update_elastic_search(prots,id,locs,prot_loc):
    es.update(index=pubmed_index, doc_type='fulltext', id=id,
            body= {
                         'doc': {'prot': prots,
                                 'loc' : locs,
                                 'prot_loc' : prot_loc

                                  }}

                )

def search(uri, term):
    query = json.dumps({
        "query": {
            "match": {
                "_id": term
            }
        }
    })
    response = requests.get(uri, data=query)
    results = json.loads(response.text)
    return results

```

After updating the elastic search with the mapper reducer script , by parsing the elastic search records and updating the elastic search records with the structure as shown in the exampe below:

![updating_es](https://github.com/IshmeetKaur/Distributed-Data-Mining-Lab/blob/master/Week12/updating_es.png)


![updating_es1](/uploads/0386e78e271d37c9b192d066a8398f44/updating_es1.png)

After storing the records in elastic search, we installed kibana and visualized the results of the analyzed datset . The screenshot of the useful relations analyzed via the kibana dashboard is shown below:

![Kibana](https://github.com/IshmeetKaur/Distributed-Data-Mining-Lab/blob/master/Week12/Kibana.png)

Some of the visualizations were:

![kibana_visualization_1.png](https://github.com/IshmeetKaur/Distributed-Data-Mining-Lab/blob/master/Week12/kibana_visualization_1.png)

![kibana_visualization_2.png](https://github.com/IshmeetKaur/Distributed-Data-Mining-Lab/blob/master/Week12/kibana_visualization_2.png)

![kibana_visualization_3](https://github.com/IshmeetKaur/Distributed-Data-Mining-Lab/blob/master/Week12/kibana_visualization_3.png)

![kibana_visualization_4.png](https://github.com/IshmeetKaur/Distributed-Data-Mining-Lab/blob/master/Week12/kibana_visualization_4.png)

![kibana_visualization_5.png](https://github.com/IshmeetKaur/Distributed-Data-Mining-Lab/blob/master/Week12/kibana_visualization_5.png)

![kibana_visualization_6](https://github.com/IshmeetKaur/Distributed-Data-Mining-Lab/blob/master/Week12/kibana_visualization_6.png)

![kibana_visualization_7](https://github.com/IshmeetKaur/Distributed-Data-Mining-Lab/blob/master/Week12/kibana_visualization_7.png)

![kibana_visualization_8](https://github.com/IshmeetKaur/Distributed-Data-Mining-Lab/blob/master/Week12/kibana_visualization_8.png)

## Troubleshooting

1)Multiple Input files in hadoop ,was givin a problem.
(https://stackoverflow.com/questions/20798888/python-mapreduce-hadop-streaming-job-that-requires-multiple-input-files)

2) hadoop-streaming: reducer doesn't seem to be running when mapred.reduce.tasks=1 was the error while running map reduce jobs .
(https://stackoverflow.com/questions/11730984/hadoop-streaming-reducer-doesnt-seem-to-be-running-when-mapred-reduce-tasks-1)

## References

1) https://github.com/titipata/pubmed_parser

2) https://www.elastic.co/de/downloads/past-releases/kibana-5-5-0

3) https://github.com/Rostlab/LocText

4) https://github.com/Rostlab/nalaf

5) https://github.com/juanmirocks/STRING-tagger-server

