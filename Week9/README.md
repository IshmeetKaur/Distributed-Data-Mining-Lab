## Storing Parsed Papers in Elastic Search: Week 9

## Description
This week we would save all the records in Elastic Search as per the [schema](https://git.rostlab.org/Lectures/ddmlab-sose17/wikis/Tasks) provided.

### Architecture 

The basic architecture of the project would be :
![parsed_papers_architecture.png](https://github.com/IshmeetKaur/Distributed-Data-Mining-Lab/blob/master/Week9/parsed_papers_architecture.png)

### Pubmed Parsing

Parsing of the records was done in the [previous week](https://github.com/IshmeetKaur/Distributed-Data-Mining-Lab/blob/master/Week8). This week the schema was provided as:

- `id` (PubMed ID of the article, if any)

- `pmc` (PubMed Central ID of the article, if any)

- `id` (unique id of the document.)

- `title` (document's title. The assumption is that every document has a title)

- `abstract` (document's abstract. Note that some abstracts allow have a hierarchy of subsections; [example](https://academic.oup.com/bioinformatics/article-lookup/doi/10.1093/bioinformatics/btx083)).

- `rest` (document's full text, if any. Again, you will have to concatenate the text parts of the entire document as you see fit. 

- `language` (document's original language. Although often English, this may not be the case. You should make sure that you _always_ use the _same_ standard language identifiers, e.g. for English maybe `en`, but really _always_ "en", and not "English", or "english", or "eng", or "en/US", etc.

- `published` (document's publication date. The year must or should always exist. If you also have the information of month and/or day, you must included it too).

- `authors` (list of author names. This is a [multivalue field](https://www.elastic.co/guide/en/elasticsearch/guide/current/complex-core-fields.html#_multivalue_fields))

- `journal` (name of the journal where the paper was published in, e.g. "Bioinformatics")

- `doi` (document's doi, if any. Juanmi's assumption is that some papers, specially older ones, will have no doi. Most recent publications should have one)

- `prots` (list of the text-mined mentions of protein names. A protein mention will be stored as an object with fields:

    - `text` (text that was found to indicate a protein mention, e.g. "p53")

    - `start_offset` (offset in the submitted string to LocText where `text` starts. Note that if your `rest` field contains all the text, including title and abstract, you just need this. But if you decided to have `rest` excluding title/abstract, you would have to incorporate some kind of sectioning information in the offset too. More complicated).

    - `end_offset`

    - `uac` (UniProt accession number for the found protein. This is a kind of unique identifier for the protein. For instance, ["p53" (human) is dentified by **P04637**](http://www.uniprot.org/uniprot/P04637)
- `locs` (list of celluar location metnions. The object follows the same structure as a protein's, only considering that cellular locations are identified by a GO identifier, e.g. ["nucleus" is identified by **GO:0005634**](http://www.ebi.ac.uk/QuickGO/GTerm?id=GO:0005634). Name this identifier field as `go`).

- `protlocs` (list of document's protein localizations as text-mined by LocText, or well even another possible future method). The relation of a protein-localization is stored as an object with fields:

    - `uac` (protein's UniProt accession number)

    - `go` (cellular location GO ID)

    - `protref` (own-generated unique reference that points to a protein mention in the list of `prots`. For example, this unique reference could be a string that concatenates the start and end offsets of the referenced protein, e.g. `protref = start + "_" + end`)

    - `locref` (analogous)
'''

### Pubmed Parsing Full Text

Since the parsing was done, we changed the internal parsing function of full text to return the body of the paper in "rest" or "text".All the records from /nfs/mnt/fulltext was taken and stored into elastic search an automation script as shown below:

![pubmed_parsing_fulltext.png](https://github.com/IshmeetKaur/Distributed-Data-Mining-Lab/blob/master/Week9/pubmed_parsing_fulltext.png)

**Language Detected** field was filled by using a library landdetect in python.
**GLob** library helped to acces the files stored in *.nxml* format.
**Unique ID** was created by concatenating the pmc_id and pm_id.


 **Everything was put in a try except block** to ensure the code didnot break.

After parsing the /nfs/mnt, 
![parsing_papers in nfs mount](https://github.com/IshmeetKaur/Distributed-Data-Mining-Lab/blob/master/Week9/parsing_papers in nfs mount.png)

The unique ids were stored as below:
![unique_ids](https://github.com/IshmeetKaur/Distributed-Data-Mining-Lab/blob/master/Week9/unique_ids.png)

After storing the pubmed records, we can fetch the records as shown below:
![fetch_records](https://github.com/IshmeetKaur/Distributed-Data-Mining-Lab/blob/master/Week9/fetch_records.png)

## Abstract Storage
The abstracts were successfully stored into elastic search with a similar script as shown above.

## Remarks
Some of the files didnot contain text giving an error.1,60,0000 files were parsed and stored correctly.
