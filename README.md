# Greek_Parliament_proceedings_dataset

#### Script order:

#### Record Collection and Cleaning:

1. __web_crawler_for_proceeding_files.py:__ Download record files from https://www.hellenicparliament.gr/Praktika/Synedriaseis-Olomeleias to the original_data folder and change the filenames to match the template "recordDate_id_periodNo_sessionNo_sittingNo.ext". This script also logs the records that are missing from the website in the file rows_with_no_files.txt.
1. __convert2txt.py:__ Convert all types of downloaded record files (pdf, doc, docx) to text format with the use of tika-app-1.20.jar and translate the filenames from Greek to English. Save the converted files to the folder _data.

#### Parliament Members Data Collection and Cleaning:
3. __web_crawler_for_parliament_members.py:__ Download information of the Parliament Members from the dropdown list at https://www.hellenicparliament.gr/Vouleftes/Diatelesantes-Vouleftes-Apo-Ti-Metapolitefsi-Os-Simera/. Write the output to the file original_parl_members_data.csv.
1. __parl_members_data_cleaner.py:__ Clean and format the file original_parl_members_data.csv for further use. Write the output to the file parl_members_activity_1989onwards.csv.
1. __add_gender_to_members.py:__ Add gender column to the parl_members_activity_1989onwards.csv with the use of the manually created files female_names_alternatives_gr.txt and male_names_alternatives_gr.txt (lists of first names and their alternatives) and create the file parl_members_activity_1989onwards_with_gender.csv.

#### Government Members Data Collection and Cleaning:

7. __greek_name_cases_wiki_crawler.py:__ Crawl the wiktionary lists of modern Greek female/male names/surnames and additionally collect all the grammatical cases, when available in tables within each entry page. The output consists of 4 json files, namely female_name_cases.json, male_name_cases.json, female_surname_cases.json, male_surname_cases.json.
1. __produce_cases_from_nominative.py:__ Take as input the json files produced from the script greek_name_cases_wiki_crawler.py and produce the missing grammatical cases based on the nominative case. It produces 4 json files, namely female_name_cases_populated.json, male_name_cases_populated.json, female_surname_cases_populated.json, male_surname_cases_populated.json.
1. __web_crawler_for_government_members.py:__ Crawl the website https://gslegal.gov.gr/?page_id=776&sort=time and collect information about all the governments from 1989 up to 2020 and all the members that were assigned government roles. Create the files governments_1989onwards.csv (with information only about the governments and their start and end dates) and the original_gov_members_data.csv with the crawled raw data from the website.
1. __gov_members_data_cleaner.py:__ Clean the file original_gov_members_data.csv. Convert names and surnames from genitive to nominative case and add gender with the use of the files crawled from Wiktionary (male_name_cases_populated.json, female_name_cases_populated.json, male_surname_cases_populated.json). Make corrections in roles, member names, entries and create a file formatted_roles_gov_members_data.csv with columns: member_name, role, role_start_date, role_end_date, gender.

#### Speech Extraction

11. __join_members_activity.py:__ Concatenate 3 files with information about the parliament members and extra parliamentary members. The input files are: parl_members_activity_1989onwards_with_gender.csv (includes elected parliament members), formatted_roles_gov_members_data.csv (includes all government members that have been assigned a government role but may not have been necessarily elected as parliament members) and extra_roles_manually_collected.csv (includes manually collected additional roles from Wikipedia such as Chairman of the Parliament, party leaders etc). An extra column is added with the name of the government during each member's activity, with the use of the file governments_1989onwards.csv. The final output of this script is the file all_members_activity.csv with columns: member_name, member_start_date, member_end_date, political_party, administrative_region, gender, roles, government_name.  
1. __member_speech_matcher.py:__ Extract speeches from record files and match them to the official parliament or government member. After the detection of a speech in a record file with the use of regular expressions, we search the detected speaker in the file all_members_activity.csv. For the string comparison between names, the manually created file greek_names_alts_only.txt is used with a list of Greek names and their alternatives. The output file is the tell_all.csv with columns: member_name, sitting_date, parliamentary_period, parliamentary_session, parliamentary_sitting, political_party, government, member_region,roles, member_gender,speaker_info, speech. This script also creates the file files_with_content_problems.txt, where record files that are skipped due to encoding issues are logged.
1. __fill_proedr_names.py:__ Fill in the names of chairmen in the same sittings when they are not mentioned in a specific line of the record file, as long as the sitting has only one chairman. The output is the file tell_all_filled.csv.
1. __csv_concat.py:__ Additional script that concatenates all tell_all.csv files created in case the script member_speech_matcher.py is run in parallel for time-optimization and created different csv files for different batches of the data. The output of this script is the file tell_all_final.csv.


#### Requirements
- Libraries from requirements.txt
- Python version 3.7.3
- WebDriver for Chrome
- tika-app-1.20.jar