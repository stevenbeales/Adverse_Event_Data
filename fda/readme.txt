Filenames and description:
standard_case_drug.tsv  - Aggregated and mapped information found in the DRUGyyQq files from LAERS and FAERS. 
standard_case_outcome.tsv - Aggregated and mapped all REACyyQq files from LAERS and FAERS.
standard_case_outcome_category.tsv -  Combined SNOMED-CT outcome concept identifier with the OUTyyQq files from LAERS and FAERS.
standard_case_indication.tsv - Mapped indication preferred terms from the INDIyyQq files into OHDSI standard vocabulary concept identifiers and SNOMEDCT concept identifiers.
standard_drug_outcome_contingency_table.tsv - Contains all calculated 2x2 contingency tables for all drug-outcome combinations found in the data.
standard_drug_outcome_count.tsv - Features the total counts for all drug-outcomes are.
standard_drug_outcome_drilldown.tsv  - Contains the mapped drug/outcome pairs found in all cases (LAERS/FAERS).
standard_drug_outcome_statistics.tsv - Features for all drug-outcome pairs the PPR and ROR with their 95% confidence interval (upper and lower values).
concept - OHDSI vocabulary version v5.0 08-JUN-15 concepts.
vocabulary - Source of OHDSI vocabularies for version v5.0 08-JUN-15.
README.TXT - This file. 

Loading instruccions:

1) Create a database/schema with your desired name.
2) Select the created database as the active one (USE <database_name>)
3) Execute the following SQL commands in order:

# AEOLUS Table creation - Run First #
CREATE TABLE concept (concept_id INT(11) NOT NULL, concept_name VARCHAR(255) NOT NULL, domain_id VARCHAR(20) NOT NULL, vocabulary_id VARCHAR(20) NOT NULL, concept_class_id VARCHAR(20) NOT NULL, standard_concept VARCHAR(1) NULL DEFAULT NULL, concept_code VARCHAR(50) NOT NULL,	valid_start_date DATE NOT NULL,	valid_end_date DATE NOT NULL,	invalid_reason VARCHAR(1) NULL DEFAULT NULL);
CREATE TABLE vocabulary (vocabulary_id VARCHAR(20) NOT NULL, vocabulary_name VARCHAR(255) NOT NULL, vocabulary_reference VARCHAR(255) NULL DEFAULT NULL, vocabulary_version VARCHAR(255) NULL DEFAULT NULL, vocabulary_concept_id INT(11) NOT NULL);
CREATE TABLE standard_case_drug (primaryid VARCHAR(512) NULL DEFAULT NULL, isr VARCHAR(512) NULL DEFAULT NULL, drug_seq VARCHAR(512) NULL DEFAULT NULL, role_cod VARCHAR(512) NULL DEFAULT NULL, standard_concept_id INT(11) NULL DEFAULT NULL);
CREATE TABLE standard_case_indication (primaryid VARCHAR(512) NULL DEFAULT NULL, isr VARCHAR(512) NULL DEFAULT NULL, indi_drug_seq VARCHAR(512) NULL DEFAULT NULL, indi_pt VARCHAR(512) NULL DEFAULT NULL, indication_concept_id INT(11) NULL DEFAULT NULL, snomed_indication_concept_id INT(11) NULL DEFAULT NULL);
CREATE TABLE standard_case_outcome (primaryid VARCHAR(512) NULL DEFAULT NULL, isr VARCHAR(512) NULL DEFAULT NULL, pt VARCHAR(512) NULL DEFAULT NULL, outcome_concept_id INT(11) NULL DEFAULT NULL,	snomed_outcome_concept_id INT(11) NULL DEFAULT NULL);
CREATE TABLE standard_case_outcome_category (primaryid VARCHAR(512) NULL DEFAULT NULL, isr VARCHAR(512) NULL DEFAULT NULL, outc_code VARCHAR(512) NULL DEFAULT NULL, snomed_concept_id INT(11) NULL DEFAULT NULL);
CREATE TABLE standard_drug_outcome_contingency_table (drug_concept_id INT(11) NULL DEFAULT NULL, outcome_concept_id INT(11) NULL DEFAULT NULL, count_a BIGINT(20) NULL DEFAULT NULL, count_b DECIMAL(10,0) NULL DEFAULT NULL, count_c DECIMAL(10,0) NULL DEFAULT NULL, count_d DECIMAL(10,0) NULL DEFAULT NULL);
CREATE TABLE standard_drug_outcome_count (drug_concept_id INT(11) NULL DEFAULT NULL, outcome_concept_id INT(11) NULL DEFAULT NULL, drug_outcome_pair_count BIGINT(20) NULL DEFAULT NULL, snomed_outcome_concept_id INT(11) NULL DEFAULT NULL);
CREATE TABLE standard_drug_outcome_drilldown (drug_concept_id INT(11) NULL DEFAULT NULL, outcome_concept_id INT(11) NULL DEFAULT NULL, snomed_outcome_concept_id INT(11) NULL DEFAULT NULL, primaryid VARCHAR(512) NULL DEFAULT NULL, isr VARCHAR(512) NULL DEFAULT NULL);
CREATE TABLE standard_drug_outcome_statistics (drug_concept_id INT(11) NULL DEFAULT NULL, outcome_concept_id INT(11) NULL DEFAULT NULL, snomed_outcome_concept_id INT(11) NULL DEFAULT NULL, case_count BIGINT(20) NULL DEFAULT NULL, prr DECIMAL(20,5) NULL DEFAULT NULL, prr_95_percent_upper_confidence_limit DECIMAL(20,5) NULL DEFAULT NULL, prr_95_percent_lower_confidence_limit DECIMAL(20,5) NULL DEFAULT NULL, ror DECIMAL(20,5) NULL DEFAULT NULL, ror_95_percent_upper_confidence_limit DECIMAL(20,5) NULL DEFAULT NULL, ror_95_percent_lower_confidence_limit DECIMAL(20,5) NULL DEFAULT NULL);
CREATE TABLE standard_unique_all_case (caseid VARCHAR(512) NULL DEFAULT NULL,	primaryid VARCHAR(512) NULL DEFAULT NULL,	isr VARCHAR(512) NULL DEFAULT NULL);
# AEOLUS Data loading - Run Second #
LOAD DATA LOCAL INFILE 'concept.tsv' INTO TABLE concept FIELDS TERMINATED BY '\t' LINES TERMINATED BY '\n' IGNORE 0 LINES;
LOAD DATA LOCAL INFILE 'vocabulary.tsv' INTO TABLE vocabulary FIELDS TERMINATED BY '\t' LINES TERMINATED BY '\n' IGNORE 0 LINES;
LOAD DATA LOCAL INFILE 'standard_case_drug.tsv' INTO TABLE standard_case_drug FIELDS TERMINATED BY '\t' LINES TERMINATED BY '\n' IGNORE 0 LINES;
LOAD DATA LOCAL INFILE 'standard_case_indication.tsv' INTO TABLE standard_case_indication FIELDS TERMINATED BY '\t' LINES TERMINATED BY '\n' IGNORE 0 LINES;
LOAD DATA LOCAL INFILE 'standard_case_outcome.tsv' INTO TABLE standard_case_outcome FIELDS TERMINATED BY '\t' LINES TERMINATED BY '\n' IGNORE 0 LINES;
LOAD DATA LOCAL INFILE 'standard_case_outcome_category.tsv' INTO TABLE standard_case_outcome_category FIELDS TERMINATED BY '\t' LINES TERMINATED BY '\n' IGNORE 0 LINES;
LOAD DATA LOCAL INFILE 'standard_drug_outcome_contingency_table.tsv' INTO TABLE standard_drug_outcome_contingency_table FIELDS TERMINATED BY '\t' LINES TERMINATED BY '\n' IGNORE 0 LINES;
LOAD DATA LOCAL INFILE 'standard_drug_outcome_count.tsv' INTO TABLE standard_drug_outcome_count FIELDS TERMINATED BY '\t' LINES TERMINATED BY '\n' IGNORE 0 LINES;
LOAD DATA LOCAL INFILE 'standard_drug_outcome_drilldown.tsv' INTO TABLE standard_drug_outcome_drilldown FIELDS TERMINATED BY '\t' LINES TERMINATED BY '\n' IGNORE 0 LINES;
LOAD DATA LOCAL INFILE 'standard_drug_outcome_statistics.tsv' INTO TABLE standard_drug_outcome_statistics FIELDS TERMINATED BY '\t' LINES TERMINATED BY '\n' IGNORE 0 LINES;
LOAD DATA LOCAL INFILE 'standard_unique_all_case.tsv' INTO TABLE standard_unique_all_case FIELDS TERMINATED BY '\t' LINES TERMINATED BY '\n' IGNORE 0 LINES;
# AEOLUS Index creation - Run third - This will be time consuming!!
ALTER TABLE concept ADD PRIMARY KEY (concept_id), ADD UNIQUE INDEX idx_concept_concept_id (concept_id), ADD INDEX idx_concept_code (concept_code), ADD INDEX idx_concept_vocabluary_id (vocabulary_id), ADD INDEX idx_concept_domain_id (domain_id), ADD INDEX idx_concept_class_id (concept_class_id);
ALTER TABLE vocabulary ADD UNIQUE INDEX idx_vocabulary_vocabulary_id (vocabulary_id);
ALTER TABLE standard_case_drug ADD INDEX idx_standard_case_drug_primary_id (primaryid(255), drug_seq(255)), ADD INDEX idx_standard_case_drug_isr (isr(255), drug_seq(255)), ADD INDEX idx_standard_case_drug_standard_concept_id (standard_concept_id);
ALTER TABLE standard_case_indication ADD INDEX idx_standard_case_indication_primary_id (primaryid(255), indi_drug_seq(255)), ADD INDEX idx_standard_case_indication_isr (isr(255), indi_drug_seq(255)), ADD	INDEX idx_standard_case_indication_indication_concept_id (indication_concept_id), ADD INDEX idx_standard_case_indication_snomed_indication_concept_id (snomed_indication_concept_id);
ALTER TABLE standard_case_outcome ADD INDEX idx_standard_case_outcome_primary_id (primaryid(255)), ADD INDEX idx_standard_case_outcome_isr (isr(255)), ADD INDEX idx_standard_case_outcome_outcome_concept_id (outcome_concept_id), ADD INDEX idx_standard_case_outcome_snomed_outcome_concept_id (snomed_outcome_concept_id);
ALTER TABLE standard_case_outcome_category ADD INDEX idx_standard_case_outcome_category_primary_id (primaryid(255)), ADD INDEX idx_standard_case_outcome_category_isr (isr(255)), ADD INDEX idx_standard_case_outcome_category_snomed_concept_id (snomed_concept_id, outc_code(255));
ALTER TABLE standard_drug_outcome_contingency_table ADD INDEX idx_standard_drug_outcome_contingency_table_drug_concept_id (drug_concept_id), ADD INDEX idx_standard_drug_outcome_contingency_table_outcome_concept_id (outcome_concept_id);
ALTER TABLE standard_drug_outcome_count ADD INDEX idx_standard_drug_outcome_count_drug_concept_id (drug_concept_id), ADD INDEX idx_standard_drug_outcome_count_outcome_concept_id (outcome_concept_id), ADD INDEX idx_standard_case_outcome_count_snomed_outcome_concept_id (snomed_outcome_concept_id);
ALTER TABLE standard_drug_outcome_drilldown ADD	INDEX idx_standard_drug_outcome_drilldown_drug_concept_id (drug_concept_id), ADD	INDEX idx_standard_drug_outcome_drilldown_outcome_concept_id (outcome_concept_id), ADD INDEX idx_standard_drug_outcome_drilldown_snomed_outcome_concept_id (snomed_outcome_concept_id), ADD INDEX idx_standard_drug_outcome_drilldown_primary_id (primaryid(255)), ADD INDEX idx_standard_drug_outcome_drilldown_isr (isr(255));
ALTER TABLE standard_drug_outcome_statistics ADD INDEX idx_standard_drug_outcome_statistics_drug_concept_id (drug_concept_id), ADD INDEX idx_standard_drug_outcome_statistics_outcome_concept_id (outcome_concept_id), ADD INDEX idx_standard_case_outcome_statistics_snomed_outcome_concept_id (snomed_outcome_concept_id);
ALTER TABLE standard_unique_all_case INDEX idx_standard_unique_all_case_caseid (caseid(255)), ADD INDEX idx_standard_unique_all_case_primary_id (primaryid(255)), ADD INDEX idx_standard_unique_all_case_isr (isr(255));

