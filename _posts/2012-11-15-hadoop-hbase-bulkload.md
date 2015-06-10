---
layout: post
title: HBase利用bulk load批量导入数据
date: 2012-11-15 20:00
author: onecoder
comments: true
categories: [bulkload, hadoop, Hadoop, hbase]
---
<p>
	<a href="http://onecoder">OneCoder</a>只是一个初学者，记录的只是自己的一个过程。不足之处还望指导。</p>
<p>
	看网上说导入大量数据，用bulk load的方式效率比较高。bulk load可以将固定格式的数据文件转换为HFile文件导入，当然也可以直接导入HFile文件。所以<a href="http://onecoder">OneCoder</a>最开始考虑的生成HFile文件供HBase导入，不过由于手太新，一直没有搞定。参考了很多网上的代码也没跑通。暂时搁浅。</p>
<p>
	后来<a href="http://onecoder">OneCoder</a>采用了，生成普通的数据格式文件，然后用过imporsttsv命令导入的方式成功。生成数据文件代码如下：</p>
<p>
	&nbsp;</p>
<p>
	&nbsp;</p>
<pre class="brush:java;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
private static final String PATH = &quot;F:/data.txt&quot;;

	/**
	 * @param args
	 * @author lihzh
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; * @alia OneCoder
         * @blog http://www.coderli.com
	 * @throws IOException 
	 * @date 2012-11-14 下午4:51:22
	 */
	public static void main(String[] args) throws IOException {
		long startTime = System.currentTimeMillis();
		File dataFile = getFile();
		FileWriter writer = null;
		try {
			writer = new FileWriter(dataFile);
			int timeCount = 1;
			int resourceCount = 1;
			for (int j = 0; j &lt; timeCount; j++) {
				long timeStamp = System.currentTimeMillis();
				for (int i = 0; i &lt; resourceCount; i++) {
					UUID uuid = UUID.randomUUID();
					String rowKey = uuid.toString() + &quot;_&quot; + timeStamp;
					Random random = new Random();
					String cpuLoad = String.valueOf(random.nextDouble())
							.substring(0, 4);
					String memory = String.valueOf(random.nextDouble())
							.substring(0, 4);
					StringBuilder builder = new StringBuilder();
					builder.append(rowKey).append(&quot;\t&quot;).append(cpuLoad)
							.append(&quot;\t&quot;).append(memory).append(&quot;\t&quot;).append(uuid.toString()).append(&quot;\t&quot;).append(timeStamp);
					writer.append(builder.toString());
					if ((i +  1) * (j + 1) &lt; timeCount * resourceCount) {
						writer.append(&quot;\r&quot;);
					}
				}
			}
			long endTime = System.currentTimeMillis();
			System.out.println(&quot;Cost Time: &quot; + (endTime - startTime));
		} catch (IOException e) {
			e.printStackTrace();
		} finally {
			writer.close();
		}
	}

	/**
	 * 得到一个新文件
	 * 
	 * @return
	 * @author lihzh
	 * @date 2012-11-14 下午4:53:31
	 */
	private static File getFile() {
		File file = new File(PATH);
		if (!file.exists()) {
			try {
				file.createNewFile();
			} catch (IOException e) {
				e.printStackTrace();
			}
		}
		return file;
	}</pre>
<div>
	文件格式大致如下：</div>
<blockquote>
	<p>
		29611690-69cb-4749-8bd5-be75793d6611_1352968490061 0.41 0.34 29611690-69cb-4749-8bd5-be75793d6611 1352968490061</p>
</blockquote>
<p>
	然后将文件上传到HDFS中，</p>
<div>
	<blockquote>
		<p>
			hadoop fs -put /home/admin/Desktop/data.txt /test</p>
	</blockquote>
	转换成HFile格式存储<br />
	<blockquote>
		<div>
			hadoop jar hbase-version.jar importtsv -Dimporttsv.columns=HBASE_ROW_KEY,c1,c2 -Dimporttsv.bulk.output=tmp hbase_table hdfs_file &nbsp;</div>
	</blockquote>
	生成HFile文件。其中c1,c2是列名，格式为:列族：列名
	<div>
		然后，导入到HBase中：</div>
	<blockquote>
		<p>
			hadoop jar hbase-version.jar completebulkload /user/hadoop/tmp/cf hbase_table</p>
	</blockquote>
	这里的路径都是hdfs的路径。</div>

