[Back to the cncf/velocity README.md file](../README.md)

[Guide to non-github project processing](non_github_repositories.md)

[Other useful notes](other_notes.md)

## Guide to the CNCF projects chart creation

`analysis.rb` can be used to create data for a Cloud Native Computing Foundation projects bubble chart such as this one
![sample chart](./cncf_chart_example.png?raw=true "CNCF projects")

The chart itself can be generated in a [google sheet](https://docs.google.com/spreadsheets/d/1jcpWHVj59dzkPBusRpyA65DBoqPll-OkJ0jJ19D6s1A/edit#gid=0).

### Chart data
Go to this [CNCF page](https://www.cncf.io/projects/) to find a list of current projects.

For every project, find a github repo and add it to a [query](BigQuery/velocity_cncf.sql) appropriately - either as an org or a single repo or both. If a project does not have a GitHub repo or only lists a mirror, skip it for now but later add manually.

Run the query for a year, for example: `./run_cncf_bq.sh 2018-04-01 2019-04-01`. It takes about 900GB and costs about $4.50.

It will generate a file for example: `data/data_cncf_projects_20180401_20190401.csv`.

Run `analysis.rb` with:
```
ruby analysis.rb data/data_cncf_projects_20180401_20190401.csv projects/projects_cncf_20180401_20190401.csv map/hints.csv map/urls.csv map/defmaps.csv map/skip.csv map/ranges_sane.csv
```
or use `shells/run_cncf.sh` which does the same, just make sure the file names are ok in the script.

To compare CNCF K8s data vs non-k8s data do `ruby analysis.rb data/data_cncf_projects_20170801_20180801.csv projects/projects_cncf_k8s_non_k8s_20170101_20180801.csv map/hints_k8s_non_k8s.csv map/urls_k8s_non_k8s.csv map/defmaps_k8s_non_k8s.csv map/skip.csv map/ranges_sane.csv`.

Make a copy of the [google sheet](https://docs.google.com/spreadsheets/d/1jcpWHVj59dzkPBusRpyA65DBoqPll-OkJ0jJ19D6s1A/edit?usp=sharing).

Put results of the analysis into a file and import the data in the 'Data' sheet in cell H1. <br />
File -> Import -> Upload -> in the Import location section, select the radio button called 'Replace data at selected cell', click Import data

Select the Chart tab, it will be updated automatically

A gist describing this process is at https://gist.github.com/lukaszgryglicki/093ced06455a3f14f0e4d25459525207

use [this sheet](https://docs.google.com/spreadsheets/d/1j_L8AL137U8R3TclNo9b79m2r0nZi3b484PAqqeY-H8/edit?usp=sharing) for K8s vs. Non-K8s comparisoion.

### CNCF Projects split by Kubernetes VS rest
For this case, a new set of map files was created:
- `map/k8s_vs_rest_defmaps.csv` - list of orgs found in query
- `map/k8s_vs_rest_urls.csv` - definition of k8s vs rest
- `map/k8s_vs_rest_hints.csv` - list of repos found in query

Lists of orgs/repos in the map files should contain all values used in any period query.

It should be noted that historically, as CNCF grows, new projects are added. To get data for 2016, a query similar to that in `BigQuery/query_cncf_4p_201511_201610.sql` should be run and the following year would be span by `BigQuery/query_cncf_projects_201611_201710.sql`.
To prepare an analysis, a command similar to this should be run:
```
ruby analysis.rb data/data_cncf_projects_201611_201710.csv projects/projects_cncf_k8s_vs_rest_201611_201710.csv map/k8s_vs_rest_hints.csv map/k8s_vs_rest_urls.csv map/k8s_vs_rest_defmaps.csv map/skip.csv map/ranges_unlimited.csv
```
