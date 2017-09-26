## Text-mine the relationship of Protein & Cell with LocText: Week 10

## Description
This week we would extract the full text records and abstracts stored in elastic search and mine the relationships using LocText.

### Elastic Search Records

In the [previous week](https://git.rostlab.org/Lectures/ddmlab-sose17/wikis/week9_dataengineers), the full text records were retrieved using the pubmed parser and stored into elastic search.

Some modifications were made in the pubmed_oa_parser.py to concatenate all the paragraphs. The screenshot of the changes made in the function **parse_pubmed_paragraph** is:


![parse_pubmed_paragraph](https://github.com/IshmeetKaur/Distributed-Data-Mining-Lab/blob/master/Week10/parse_pubmed_paragraph.png)

The automation script described in the [section](https://git.rostlab.org/Lectures/ddmlab-sose17/wikis/week9_dataengineers#pubmed-parsing-full-text) of the previous week was changed to include the 
*  Title
*  Abstract
*  All Paragraphs

The screenshot of the changed script is:

![pubmed_full_text_parser](https://github.com/IshmeetKaur/Distributed-Data-Mining-Lab/blob/master/Week10/pubmed_full_text_parser.png)

The **rest field** now contains the following (for a particular id- **pmid_pmcid**)

![result_es](https://github.com/IshmeetKaur/Distributed-Data-Mining-Lab/blob/master/Week10/result_es.png)

All the records were stored in the index pubmedfulltextfinal are:

![all_records__1_](https://github.com/IshmeetKaur/Distributed-Data-Mining-Lab/blob/master/Week10/all_records__1_.png)

As seen from the screenshot above, the pubmedfulltextfinal index has only **11059 records**. This is because the records after storage to elastic search , gave an error of **Out of memory** on the master. So, we initially stored only 11059 for the initial mining of records with the help of LocText.

### Mining with LocText

The [LocText](https://git.rostlab.org/Lectures/ddmlab-sose17/wikis/week8_dataengineers#loctext-installation-1) installed in week 8 was explored to extract the **rest** key from elastic search and provide as an input to **LocText** with the help of the script [run.py](https://github.com/Rostlab/LocText/blob/develop/run.py). The script was modified to take as an input **pmid or text** from elastic search without passing command line arguments in the form shown below:

![python_run](https://github.com/IshmeetKaur/Distributed-Data-Mining-Lab/blob/master/Week10/python_run.png)

Instead of using the command (--text "text") to pass the text for mining, we created a script called **editedrun.py** and a repository called **usingLocText**.This repository would run the editedrun script from LocText by giving as an input the text retrieved from "rest" key.

**Scripts**

The script edited run.py is 

![editedRun](https://github.com/IshmeetKaur/Distributed-Data-Mining-Lab/blob/master/Week10/editedRun.png)

 After running the script, we get as a result the predicted NalafDatstructure of Predicted Relations of the Proteins and the locations . Running the script without giving the arguments :

![edited_run](https://github.com/IshmeetKaur/Distributed-Data-Mining-Lab/blob/master/Week10/edited_run.png)

**UsingLocText**

We created a repository called **UsingLocText** which imports the LocText editedrun.py described above and queries elasticsearch to retrieve the text to be queried.

![runusing](https://github.com/IshmeetKaur/Distributed-Data-Mining-Lab/blob/master/Week10/runusing.png)

This script successfully queries elastic search and returns the predicted results with a type of **NalafDatstructure**.

## Troubleshooting

1. Import error of named entities in Loctext if the String Tagger Server is not on.
2. If the text as an input to LocText is from elastic search (rest) field, there is an error. But if the text is containing a protein (for e.g "GCN2 was constitutively localized to the nucleolus or recruited to the nucleolus by amino acid starvation stress"), the script (editedRun.py) runs successfully.
3. How to use the return type i.e [NalafDatastructure](https://github.com/Rostlab/nalaf/blob/develop/nalaf/structures/data.py) in the reducer stage for the hadoop jobs?

## References

1. https://github.com/Rostlab/LocText#sample-script
2. https://github.com/Rostlab/LocText/blob/develop/run.py
3. https://github.com/Rostlab/nalaf/blob/develop/nalaf/structures/data.py