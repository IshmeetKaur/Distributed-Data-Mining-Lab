## Extraction of NCBI Database : Part1 - Week 6

## Description
This week we would explore extraction of abstracts and papers from the NCBI Database. 

### [NCBI API](https://www.nlm.nih.gov/databases/download/pubmed_medline.html)

The National Center for Biotechnology Information (https://www.ncbi.nlm.nih.gov/) provides access to biomedical and genomic information. NCBI provides several public APIs that allow programmatic access to many databases and tools.

Entrez Programming Utilities (E-utilities)

The E-utilities are the public API to the NCBI Entrez system and allow access to all Entrez databases including PubMed, PMC, Gene, Nuccore and Protein. A companion package named Entrez Direct consists of several executables that allow the E-utilities to be called directly from a UNIX command line.

 To install the EDirect software:

```  
  cd ~
  perl -MNet::FTP -e \
    '$ftp = new Net::FTP("ftp.ncbi.nlm.nih.gov", Passive => 1);
     $ftp->login; $ftp->binary;
     $ftp->get("/entrez/entrezdirect/edirect.zip");'
  unzip -u -q edirect.zip
  rm edirect.zip
  export PATH=$PATH:$HOME/edirect
  ./edirect/setup.sh
```
[Navigation functions](https://www.ncbi.nlm.nih.gov/books/NBK179288/) to explore the databases include esearch, elink , efilter, efetch, xtract, einfo and epost .

![Screenshot2-ncbi](https://github.com/IshmeetKaur/Distributed-Data-Mining-Lab/blob/master/Week6/Screenshot2-ncbi.png)

### Entering Query Commands

In order to begin an Entrez search, the user types "esearch" and then enters the required -db (database) and -query arguments. A query on unqualified search terms:

```
  esearch -db pubmed -query "opsin gene conversion"
```
constructs the appropriate Entrez Utilities (E-utilities) URL from the query terms and executes the search.

![Esearch.png](https://github.com/IshmeetKaur/Distributed-Data-Mining-Lab/blob/master/Week6/Esearch.png)

### Retrieving PubMed Reports

Piping PubMed query results to efetch and specifying the "abstract" format:

```
  esearch -db pubmed -query "lycopene cyclase" |
  efetch -format abstract
```
returns a set of reports that can be read by a person:

![efetch_set_of_reports](https://github.com/IshmeetKaur/Distributed-Data-Mining-Lab/blob/master/Week6/efetch_set_of_reports.png)

Using "efetch -format medline" instead produces a report that can be entered into common bibliographic management software packages:

![efetch_format_medline](https://github.com/IshmeetKaur/Distributed-Data-Mining-Lab/blob/master/Week6/efetch_format_medline.png)

Entrez provides a document summary in structured XML format for every record. Piping a query to "efetch -format docsum" will generate an XML document summary set.

```
  esearch -db pubmed -query "Garber ED [AUTH] AND PNAS [JOUR]" |
  elink -related |
  efilter -query "mouse" |
  efetch -format docsum
```

![document_summary_4](https://github.com/IshmeetKaur/Distributed-Data-Mining-Lab/blob/master/Week6/document_summary_4.png)

### Exploration of XML Sets

Individual PubmedArticle objects can be retrieved directly by efetch:

```
 efetch -db pubmed -id 20643751 -format xml
```
![efetch_by_id_xml_5__1_](https://github.com/IshmeetKaur/Distributed-Data-Mining-Lab/blob/master/Week6/efetch_by_id_xml_5__1_.png)

Efetch helped to retrieve pubmed full text xml document by a single filter or a query .
In order to do bulk download, we would use the python SDK (https://github.com/ElsevierDev/elsapy). The prerequisites for the same were:

```
elsapy 
Anaconda 3.6
```

### Troubleshooting

1."Https" was not supported.

![troubleshooting_https.png](https://github.com/IshmeetKaur/Distributed-Data-Mining-Lab/blob/master/Week6/troubleshooting_https.png)

To solve this issue, we installed the following

```
apt-get install libcrypt-ssleay-perl libssl-dev liblwp-protocol-https-perl
cpanm Crypt::SSLeay IO::Socket::SSL::Utils LWP::Protocol::https
```

### References

1. https://www.ncbi.nlm.nih.gov/pmc/tools/get-full-text/
2. https://www.nlm.nih.gov/databases/download/pubmed_medline.html
3. https://dataguide.nlm.nih.gov/edirect/efetch.html