---
name: 
tagline: <u>EX</u>traction, <u>S</u>eparation, and <u>C</u>aption-based natural <u>L</u>anguage <u>A</u>nnotation of <u>IM</u>ages from scientific figures.
abstract:  Recent improvements in image resolution and acquisition speed have lead to an explosion of published imaging data in materials microscopy. Intensive human efforts, however, are still required to aggregate such published images for use in data-driven research and decision-making pipelines. EXSCLAIM! is a Python toolkit for automatically extracting figure and caption data from open source scientific documents at high volume, as well as for constructing self-annotated materials imaging datasets. 
cover_path: "assets/images/exsclaim_pano.png"
---

![image](https://drive.google.com/uc?export=view&id=142XkACsDxT9r9VgVg0RUsVvjJhaBqRIs){:width="92%"}

**EX**traction, **S**eparation, and **C**aption-based natural **L**anguage **A**nnotation of **IM**ages from scientific figures
[[wiki](https://gitlab.com/MaterialEyes/exsclaim/wikis/home)] [[paper](https://)]

## Getting started
This repo is currently private in order to protect the MaterialEyes&copy; source code while under development. 
Contact <a href="eschwenk@u.northwestern.edu">me</a> if you are are a potential user or developer and I will set you up with the proper permissions to view/contribute to the code.

## Example Usage
### <font color='#5EC6AA'>Formulate a JSON search query </font>
A JSON search query is the singular point-of-entry for using the EXSCLAIM! search and retrieval tools. Here we will query [Nature](https://www.nature.com) journals to find figures related to HAADF-STEM images of gold nanoparticles. Limiting the results to the top 5 most relevant hits, the query might look something like:

```json
{   
    "name": "nature-haadf-gold-NP",
    "journal_family": "nature",
    "maximum_scraped": 5,
    "sortby": "relevant",
    "query":
    {
        "search_field_1":
        {
            "term":"gold nanoparticle",
            "synonyms":["Au nanoparticle","Au NP"]
        },
        "search_field_2": 
        {
            "term":"HAADF-STEM",
            "synonyms":["HAADF image"]
        }
    },
    "results_dir": "extracted/nature-haadf-gold-NP/"
}
```

Saving the query avoids having to completely reformulate the structure with each new search and establishes provenance for the extraction results. Additional JSON search query examples can be found in [test](https://github.com/eschwenk/exsclaim-prerelease/tree/master/test) in the root directory.

### <font color='#5EC6AA'>Create an annotated materials imaging dataset from extracted figures</font>
With the query from above (<code>nature-haadf-gold-NP.json</code>), we extract relevant figures and construct a dataset of annotated images by using a <code>Pipeline</code> class to implement a <code>JournalScraper</code>, <code>CaptionSeparator</code> and <code>FigureSeparator</code> interface (in this order).

```python
from exsclaim.pipeline import Pipeline 
from exsclaim.tool import *

# Set query path
query_path = "query/nature-haadf-gold-NP.json"

# Initialize EXSCLAIM! tool(s)
js = JournalScraper()
cs = CaptionSeparator()
fs = FigureSeparator()

# Define run order in a tools list
tools = [js,cs,fs] 

# Initialize EXSCLAIM! pipeline
exsclaim_pipeline = Pipeline(query_path=query_path)

# Run the tools through the pipeline
exsclaim_pipeline.run(tools) 

# Save image and label (.csv) results to file
exsclaim_pipeline.to_file()
```

Calling <code>to_file()</code> saves extracted images into folders created for each figure, and prints a 'labels.csv' which includes entries for each image and its respective annotation. For a more concise record of the search results, an 'exsclaim.json' is also created, which records urls for the extracted figures, as well as bounding box information, and the associated caption text for each detected image.

## Example Output


## Acknowledgements <a name="credits"></a>
