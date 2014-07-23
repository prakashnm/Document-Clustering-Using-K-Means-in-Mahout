Document-Clustering-Using-K-Means-in-Mahout
===========================================

K-Means under Mahout and Hadoop environment to ﬁnd clusters for the 20newsgroup data:

Each indvidual document here becomes a data point and K-means algorithm can be applied to ﬁnd clusters. The number of clusters (topics) among these documents is 20, therefore K is set to be 20 before you run the algorithm. Each document will be represented as a vector using tf-idf of each unique word. Remove all stop words, which can be found here:
http://jmlr.org/papers/volume5/lewis04a/a11-smart-stop-list/english.stop

Below are the commands and steps followed for clustering.

- Run the below command to push the document  in the current directory to hdfs location /user/../input/outseqdir

	hadoop fs -put . /user/../input/outseqdir

- Run the below command to convert the text file to sequencial files.

	bash /opt/mahout/mahout-distribution-0.7/bin/mahout seqdirectory -i/user/../input/all_files -o /user/../input/outseqdir -c UTF-8 -chunk 5 																																																																																																	
- Run the below command to create sparse vector that has dictionary file, tfidf file, word count file and other files.

	bash /opt/mahout/mahout-distribution-0.7/bin/mahout seq2sparse -i /user/../input/outseqdir/* -o /user/../input/vectors --maxDFPercent 85 --namedVector -a org.hadoop.assignment3.mahout.MyFilter

- Run the below command to invoke k-mean cluster. First command, i.e. without -a option will invoke default analyzer to remove the stop words. Second command with -a org.apache.lucene.analysis.standard.StandardAnalyzer will invoke standardanalyzer to remove predefined stop words. Third command with -a org.test.MyCustomAnalyzer will invoke my own analyzer to remove stop words.

	bash /opt/mahout/mahout-distribution-0.7/bin/mahout kmeans -i /user/../input/vectors/tfidf-vectors -c /home/../assignment3/clustercentroids -o/home/../assignment3/kmeans -dm org.apache.mahout.common.distance.CosineDistanceMeasure -x10 -k20 -ow --clustering

	bash /opt/mahout/mahout-distribution-0.7/bin/mahout kmeans -i /user/../input/vectors/tfidf-vectors -c /home/../assignment3/clustercentroids -o/home/../assignment3/kmeans -dm org.apache.mahout.common.distance.CosineDistanceMeasure -x10 -k20 -ow --clustering -a org.apache.lucene.analysis.standard.StandardAnalyzer 

bash /opt/mahout/mahout-distribution-0.7/bin/mahout kmeans -i /user/../input/vectors/tfidf-vectors -c /home/../assignment3/clustercentroids -o/home/prakash/assignment3/kmeans -dm org.apache.mahout.common.distance.CosineDistanceMeasure -x10 -k20 -ow --clustering -a org.test.MyCustomAnalyzer

- Run the below command to dump the output of cluster to local file system. The file /home/../clusterdump.txt now have document point and cluster id.

	bash /opt/mahout/mahout-distribution-0.7/bin/mahout clusterdump -i/home/../assignment3/kmeans/clusters-2-final/part-r-00000 -o/home/../clusterdump.txt -d/user/../input/vectors/dictionary.file-0 -dt sequencefile -b 100 -n 20 --evaluate -dm org.apache.mahout.common.distance.CosineDistanceMeasure -sp 0 --pointsDir /home/../assignment3/kmeans/clusteredPoints/part-m-00000

- Below additional commands are performed to dump the vector points to local file system.

	bash /opt/mahout/mahout-distribution-0.7/bin/mahout seqdumper -i /home/../assignment3/kmeans/clusteredPoints/part-m-00000 >/home/../clusterdocs.txt

	bash /opt/mahout/mahout-distribution-0.7/bin/mahout seqdumper -i /home/prakash/assignment3/kmeans/clusters-2-final/part-r-00000 >/home/prakash/cluster-2-final.txt

	bash /opt/mahout/mahout-distribution-0.7/bin/mahout seqdumper -i /home/../assignment3/kmeans/clusters-1/part-r-00000 >/home/../cluster-1.txt



