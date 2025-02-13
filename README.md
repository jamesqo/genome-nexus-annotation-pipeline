# Genome Nexus Annotation Pipeline
These tools allow for annotation of genomic variants from a MAF for import into
the cBioPortal using [Genome Nexus](http://genomenexus.org).

## MAF Annotation
The `annotationPipeline` module is a command line tool to annotate a maf using genome nexus. 

**Pre-build steps**

Create your `application.properties`:

```
cp annotationPipeline/src/main/resources/application.properties.EXAMPLE annotationPipeline/src/main/resources/application.properties
```

If you have your own
installation of Genome Nexus, you can point to it by modifying the
`application.properties` file located in
`annotationPipeline/src/main/resources`.

Create your `log4j.properties`:

```
cp annotationPipeline/src/main/resources/log4j.properties.EXAMPLE annotationPipeline/src/main/resources/log4j.properties
```

Modify the property `log4j.appender.a.File` in your `log4j.properties` file to the desired log file path.

To use it, build the project using maven and run it like so:
    
    mvn clean install
    $JAVA_HOME/bin/java -jar annotationPipeline/target/annotationPipeline-*.jar \
        --filename <INPUT_MAF> \
        --output-filename <OUTPUT DESTINATION> \
        --isoform-override <mskcc or uniprot>
    
To output error reporting to a file, supply the `-e` option a location for the file to be saved. By running the jar without any arguments or by providing the optional parameter `-h` you can view the full usage statement. 

## Annotate data with Docker
Genome Nexus Annotation Pipeline is available on Docker: https://hub.docker.com/r/genomenexus/gn-annotation-pipeline.

#### Usage instruction
```
docker run -v ${PWD}:/wd genomenexus/gn-annotation-pipeline:master --filename /wd/input.txt  --output-filename /wd/output.txt
```
- `-v ${PWD}:/wd`: This option maps the current working directory to a volume within the Docker container at the path `/wd`. This makes it possible for files in the host directory to be accessed and modified between container and host.
- `--filename /wd/input.txt`: This option specifies the input file location at `/wd/input.txt`, which should be under the same directory as output file.

- `--output-filename /wd/output.txt`: This option specifies the output file where the annotated results will be saved. The file will be created at `/wd/output.txt`, which should be under the same directory as input file.

#### View logging file
To enable logging in the Genome Nexus Annotation Pipeline and access the log file, you need to mount your local path to view the log file locally. By default, the log file is stored at `/genome-nexus-annotation-pipeline/logs/genome-nexus-annotation-pipeline.log`. Use the following command as an example:
```
docker run  -v ${PWD}:/wd -v ${PWD}/logs:/genome-nexus-annotation-pipeline/logs gn-annotation-pipeline:master --filename  /wd/input.txt  --output-filename /wd/output.txt
```
- `-v ${PWD}/logs:/genome-nexus-annotation-pipeline/logs` flag mounts the logs directory inside the current working directory to the corresponding directory inside the Docker container.
- Other flags are the same as above

Make sure to adjust the file paths according to your specific requirements. Once the command is executed, the log file will be generated and stored in the logs directory within your local directory. 

### Optional parameters
| Short | Long | Description | 
| ------ | ------  | ------ |
| `-h` | `--help` | shows this help document and quits|
| `-f` | `--filename` |Mutation filename|
| `-o` | `--output-filename` | Output filename (including path)|
| `-t` | `--output-format`  | extended, minimal or a file path which includes output format (FORMAT EXAMPLE: Chromosome,Hugo_Symbol,Entrez_Gene_Id,Center,NCBI_Build)|
| `-i` | `--isoform-override` | Isoform Overrides. Options: mskcc or uniprot|
| `-e` | `--error-report-location` | Error report filename (including path)|
| `-r` | `--replace-symbol-entrez` | Replace gene symbols and entrez id with what is provided by annotator, this is enabled by default|
| `-p` | `--post-interval-size` | Number of records to make POST requests to Genome Nexus with at a time |
| `-s` | `--strip-matching-bases` | Strip matching allele bases. Options: first, all, none. For example: AAC/AAT, strip-off first: AC/AT, strip-off all: C/T, strip-off none: AAC/AAT  |
| `-a` | `--add-original-genomic-location` | Add original genomic location data columns into the output, name columns with prefix 'IGNORE_Genome_Nexus_Original_'). This would be useful if saving a reference of original input is needed and won't be changed in any condition|
| `-d` | `--ignore-original-location` | Genome-nexus-annotation-pipeline reads original genomic location info as input by default, if not existing, reading from normal genomic location info columns. Adding `-d` ignores original genomic location info columns (columns with prefix 'IGNORE_Genome_Nexus_Original_') and only use whatever in normal genomic location info columns. This would be helpful if you'd like to stick with current genomic location info columns.|

### Annotation fields
| Field | Source | Note |
| --| -- | -- |
| Hugo_Symbol |||
|Entrez_Gene_Id|||
|Center|||
|NCBI_Build|||
|Chromosome|GN response / Input||
|Start_Position|GN response / Input||
|End_Position|GN response / Input||
|Strand|||
|Consequence|||
|Variant_Classification|||
|Variant_Type|||
|Reference_Allele|GN response / Input||
|Tumor_Seq_Allele1|||
|Tumor_Seq_Allele2|GN response / Input||
|dbSNP_RS|||
|dbSNP_Val_Status|||
|Tumor_Sample_Barcode|||
|Matched_Norm_Sample_Barcode|||
|Match_Norm_Seq_Allele1|||
|Match_Norm_Seq_Allele2|||
|Tumor_Validation_Allele1|||
|Tumor_Validation_Allele2|||
|Match_Norm_Validation_Allele1|||
|Match_Norm_Validation_Allele2|||
|Verification_Status|||
|Validation_Status|||
|Mutation_Status|||
|Sequencing_Phase|||
|Sequence_Source|||
|Validation_Method|||
|Score|||
|BAM_File|||
|Sequencer|||
|t_ref_count|||
|t_alt_count|||
|n_ref_count|||
|n_alt_count|||
|HGVSc|||
|HGVSp|||
|HGVSp_Short|||
|Transcript_ID|||
|RefSeq|VEP||
|Protein_position|VEP||
|Codons|VEP||
|Exon_Number|VEP||
|gnomAD_AF|myvariant.info|Need to add "my_variant_info" in "genomenexus.enrichment_fields"|
|gnomAD_AFR_AF|myvariant.info|Need to add "my_variant_info" in "genomenexus.enrichment_fields"|
|gnomAD_AMR_AF|myvariant.info|Need to add "my_variant_info" in "genomenexus.enrichment_fields"|
|gnomAD_ASJ_AF|myvariant.info|Need to add "my_variant_info" in "genomenexus.enrichment_fields"|
|gnomAD_EAS_AF|myvariant.info|Need to add "my_variant_info" in "genomenexus.enrichment_fields"|
|gnomAD_FIN_AF|myvariant.info|Need to add "my_variant_info" in "genomenexus.enrichment_fields"|
|gnomAD_NFE_AF|myvariant.info|Need to add "my_variant_info" in "genomenexus.enrichment_fields"|
|gnomAD_OTH_AF|myvariant.info|Need to add "my_variant_info" in "genomenexus.enrichment_fields"|
|gnomAD_SAS_AF|myvariant.info|Need to add "my_variant_info" in "genomenexus.enrichment_fields"|
|MA:FIS|Mutation Assessor|Need to add "mutation_assessor" in "genomenexus.enrichment_fields"|
|MA:FImpact|Mutation Assessor|Need to add "mutation_assessor" in genomenexus.enrichment_fields"|
|MA:link.MSA|Mutation Assessor|Need to add "mutation_assessor" in "genomenexus.enrichment_fields"|
|MA:link.PDB|Mutation Assessor|Need to add "mutation_assessor" in "genomenexus.enrichment_fields"|
|Polyphen_Prediction|Polyphen|Need to add "polyphen" in "genomenexus.enrichment_fields"|
|Polyphen_Score|Polyphen|Need to add "polyphen" in "genomenexus.enrichment_fields"|
|SIFT_Prediction|SIFT|Need to add "sift" in "genomenexus.enrichment_fields"|
|SIFT_Score|SIFT|Need to add "sift" in "genomenexus.enrichment_fields"|
|Ref_Tri||Need to add "nucleotide_context" in "genomenexus.enrichment_field"|
|Var_Tri||Need to add "nucleotide_context" in "genomenexus.enrichment_field"|
|oncokb_geneExist|OncoKb|Need to add "oncokb" in "genomenexus.enrichment_fields" and provide your OncoKB token in "oncokb.token"|
|oncokb_highestDXLevel|OncoKb|Need to add "oncokb" in "genomenexus.enrichment_fields" and provide your OncoKB token in "oncokb.token"|
|oncokb_highestPXLevel|OncoKb|Need to add "oncokb" in "genomenexus.enrichment_fields" and provide your OncoKB token in "oncokb.token"|
|oncokb_highestResistanceLevel|OncoKb|Need to add "oncokb" in "genomenexus.enrichment_fields" and provide your OncoKB token in "oncokb.token"|
| oncokb_highestSensitiveLevel |OncoKb|Need to add "oncokb" in "genomenexus.enrichment_fields" and provide your OncoKB token in "oncokb.token"|
| oncokb_mutationEffect|OncoKb|Need to add "oncokb" in "genomenexus.enrichment_fields" and provide your OncoKB token in "oncokb.token"|
|oncokb_mutationEffectCitations|OncoKb|Need to add "oncokb" in "genomenexus.enrichment_fields" and provide your OncoKB token in "oncokb.token"|
| oncokb_oncogenic|OncoKb|Need to add "oncokb" in "genomenexus.enrichment_fields" and provide your OncoKB token in "oncokb.token"|
| oncokb_variantExist   |OncoKb|Need to add "oncokb" in "genomenexus.enrichment_fields" and provide your OncoKB token in "oncokb.token"|
|Annotation_Status||FAILED / SUCCESS|


### Add additional annotation columns
Genome Nexus supports additional annotation columns with the setting of "enrichment_fields". The configuration for these enrichment fields is managed through the `application.properties` file, please refer to `Pre-build steps` section.
To configure the enrichment fields, you need to include the desired field names from the provided list in the `-Dgenomenexus.enrichment_fields=` parameter of the command line, or directly add field names in `genomenexus.enrichment_fields=` in `application.properties` file. Multiple field names can be specified by separating them with commas. `annotation_summary` is highly recommended to add as default since it's crucial for lots of annotation fields.
**Example**:
```
java 
-Dgenomenexus.enrichment_fields=annotation_summary,my_variant_info 
-jar annotationPipeline/target/annotationPipeline-*.jar \ -r \ 
--filename test/data/minimal_example.in.txt \ 
--output-filename test/data/minimal_example.out.uniprot.txt \ 
--isoform-override uniprot
```
##### Available enrichment fields:
- annotation_summary:
    - This field is essential for most annotation processes.
- my_variant_info
    - This field provides gnomAD information. Genome Nexus caches static myvariant.info annotation into database.  Upon adding `my_variant_info` to the `enrichment_fields`, Genome Nexus will query the existing variant information from the myvariant.info cache within the database for relevant variants. For new variants, Genome Nexus will retrieve the data from myvariant.info and store it in the database.
- polyphen
- sift
- mutation_assessor
    - 'mutation_assessor' provides V3 version annotation from Mutation Assessor, which is currently stored in Genome Nexus database
- nucleotide_context
- oncokb:
    - 'oncokb' provides annotations of the biological consequences and clinical implications from OncoKB website. OncoKB token is required (see more information from: [https://www.oncokb.org/apiAccess](https://www.oncokb.org/apiAccess)). Please also provide your token in `-Doncokb.token=abc123` command line parameter, or directly add `oncokb.token=abc123`in the `application.properties`. No OncoKB annotation columns will be added if no valid token is provided

### Minimal MAF Example

For an example minimal input file see
[test/data/minimal_example.in.txt](test/data/minimal_example.in.txt) and
corresponding output
[test/data/minimal_example.out.uniprot.txt](test/data/minimal_example.out.uniprot.txt).
The output file was generated with:

    $JAVA_HOME/bin/java -jar annotationPipeline/target/annotationPipeline-*.jar \
        -r \
        --filename test/data/minimal_example.in.txt  \
        --output-filename test/data/minimal_example.out.uniprot.txt \
        --isoform-override uniprot


## Direct Database Annotation
If you have data already loaded into a cBioPortal database but did not properly
annotate it or discovered issues later, you can use the `databaseAnnotator`
utility to fix it.

You will need to specify some database connection parameters inside the
`application.properties` file located in
`databaseAnnotator/src/main/resources`. Once this is done, build the project
using maven and run like so:

    $JAVA_HOME/bin/java -jar databaseAnnotator/target/databaseAnnotator-*.jar \
        --isoform <mskcc or uniprot>

As with the above tool, running the jar without any arguments or by providing
the optional parameter `-h` will bring up the full usage statement. You can
also specify a single study or set of studies to annotate by using the
`--studies` parameter.

## Annotator
The `annotator` module is the client code that makes calls to the Genome Nexus
server and interprets the response. The other two modules use this as a
dependency.

## Updating the Genome Nexus Annotation Pipeline
The annotation pipeline uses models brought in by the auto-generated **genome-nexus-java-api-client** [here](https://github.com/averyniceday/genome-nexus-annotation-pipeline/blob/2356cad06aa602ee423b08d5792c50e903200a1d/pom.xml#L77) and [here](https://github.com/averyniceday/genome-nexus-annotation-pipeline/blob/2356cad06aa602ee423b08d5792c50e903200a1d/pom.xml#L82) . 

In the event of an update to the Genome Nexus server (e.g adding to an existing model), the `genome-nexus-java-api-client` must be regenerated and propagated to this codebase. To generate the newest `genome-nexus-java-api-client`, refer to instructions [here](https://github.com/genome-nexus/genome-nexus-java-api-client/blob/master/README.md). 

Once updated, update the pom with the newest commit hash from the `genome-nexus-java-api-client` codebase.
