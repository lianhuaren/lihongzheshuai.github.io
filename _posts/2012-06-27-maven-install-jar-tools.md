---
layout: post
title: Maven批量安装本地Jar文件小工具
date: 2012-06-27 21:51
author: onecoder
comments: true
categories: [Maven, Maven]
---
<p style="padding: 0px 0px 15px; margin: 0px; color: rgb(44, 44, 44); font-family: 宋体, 'Arial Narrow', arial, serif; font-size: 14px; line-height: 28px; ">
	<span style="padding: 0px; margin: 0px; font-family: Helvetica, Tahoma, Arial, sans-serif; line-height: 25px; ">Maven&nbsp; 批量安装本地 Jar文件到本地Maven库小程序。<br />
	<br />
	根据自己的需求临时开发完成。使用方式：<br />
	在config.properties中，配置待安装jar文件的存放路径。<br />
	安装时groupId,artifactId,version等信息，根据jar文件的文件名获得。采用分割&quot;-&quot;的方式，解析出来。<br />
	所以，推荐jar的文件名中含有两个&quot;-&quot;。例如：group-artifact-version.jar。<br />
	如果为group-artifactversion.jar，则groupId=group，artifactId=version=artifactversion。</span></p>
<br />
<pre class="brush:java;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
<span style="padding: 0px; margin: 0px; font-family: Helvetica, Tahoma, Arial, sans-serif; line-height: 25px; ">public class Main { 
     
    private static final Log _log = LogFactory.getLog(Main.class); 
    private static PropertyHelper propHelper = new PropertyHelper(&quot;config&quot;); 
    private static Runtime _runRuntime = Runtime.getRuntime(); 
    private static boolean isDelete = Boolean.valueOf(propHelper.getValue(&quot;delete-installed-jar&quot;)); 
    private static boolean isMove = Boolean.valueOf(propHelper.getValue(&quot;move-installed-jar&quot;)); 
    private static final String KEY_JARPATH = &quot;jar-path&quot;; 
    private static final String KEY_BACKUPPATH = &quot;back-path&quot;; 
    private static final String ENCODE = &quot;gbk&quot;; 
    private static final String INSTALL_PATH = propHelper.getValue(KEY_JARPATH); 
    private static  String CMD_INSTALL_FILE; 
    private static  String CMD_BACKUP_JAR; 
     
    public static void main(String[] args) { 
         
        _log.info(&quot;The path of the jars is [&quot;+INSTALL_PATH+&quot;].&quot;); 
        File file = new File(INSTALL_PATH); 
        if(!file.isDirectory()){ 
            _log.warn(&quot;The path must be a directory.&quot;); 
            return; 
        } 
        FilenameFilter filter = new JarFilter(); 
        File[] jarFiles = file.listFiles(filter); 
        for(File jar: jarFiles){ 
            installJarToMaven(jar); 
            if(isDelete){ 
                _log.info(&quot;Delete the original jar file [&quot;+jar.getName()+&quot;].&quot;); 
                jar.delete(); 
            }else{ 
                if(isMove){ 
                    String backupPath = propHelper.getValue(KEY_BACKUPPATH); 
                    backupJar(jar,file,backupPath); 
                } 
            } 
        } 
    } 
 
    private static void backupJar(File jar, File file, String backupPath) { 
        CMD_BACKUP_JAR = &quot;copy &quot;+INSTALL_PATH+File.separator+jar.getName()+&quot; &quot;+backupPath; 
        String[] cmds = new String[]{&quot;cmd&quot;, &quot;/C&quot;,CMD_BACKUP_JAR}; 
        try { 
            Process process =_runRuntime.exec(cmds,null,file); 
            printResult(process); 
        } catch (IOException e) { 
            e.printStackTrace(); 
        } 
            _log.info(&quot;The jar [&quot;+jar.getName()+&quot;]  is backup, it&#39;s will be deleted.\r&quot;); 
            jar.delete(); 
    } 
 
    private static void installJarToMaven(File file) { 
        String fileName = file.getName(); 
        String jarName = getJarName(fileName); 
        String groupId=null; 
        String artifactId=null; 
        String version=null; 
        int groupIndex = jarName.indexOf(&quot;-&quot;); 
        if(groupIndex==-1){ 
            version = artifactId = groupId = jarName; 
        }else{ 
            groupId = jarName.substring(0,groupIndex); 
            int versionIndex = jarName.lastIndexOf(&quot;-&quot;); 
            if(groupIndex==versionIndex){ 
                version = artifactId = jarName.substring(versionIndex+1,jarName.length()); 
            }else{ 
                artifactId = jarName.substring(groupIndex+1,versionIndex); 
                version = jarName.substring(versionIndex+1,jarName.length()); 
            } 
        } 
        _log.info(&quot;Jar [&quot;+jarName+&quot;] will be installed with the groupId=&quot;+groupId+&quot; ,&quot; 
                +&quot;artifactId=&quot;+artifactId+&quot; , version=&quot;+version+&quot;.&quot;); 
        executeInstall( groupId,  artifactId,  version, file.getPath()); 
    } 
 
    private static void executeInstall(String groupId, String artifactId, 
            String version, String path) { 
        CMD_INSTALL_FILE = createInstallFileCMD( groupId,  artifactId, 
                 version,  path); 
        String[] cmds = new String[]{&quot;cmd&quot;, &quot;/C&quot;,CMD_INSTALL_FILE}; 
        try { 
            Process process = _runRuntime.exec(cmds); 
            printResult(process); 
        } catch (IOException e) { 
            e.printStackTrace(); 
        } 
    } 
     
    private static void printResult(Process process) throws IOException { 
        InputStream is = process.getInputStream(); 
        BufferedReader br = new BufferedReader(new InputStreamReader(is,ENCODE)); 
        String lineStr; 
        while((lineStr= br.readLine()) !=null){ 
            System.out.println(lineStr); 
        } 
    } 
 
    private static String createInstallFileCMD(String groupId, 
            String artifactId, String version, String path) { 
        StringBuffer sb = new StringBuffer(); 
        sb.append(&quot;mvn install:install-file -DgroupId=&quot;).append(groupId) 
            .append(&quot; -DartifactId=&quot;).append(artifactId) 
            .append(&quot; -Dversion=&quot;).append(version) 
            .append(&quot; -Dpackaging=jar&quot;) 
            .append(&quot; -Dfile=&quot;).append(path); 
        _log.debug(sb.toString()); 
        return sb.toString(); 
    } 
 
    private static String getJarName(String fileName) { 
        int index = fileName.indexOf(&quot;.jar&quot;); 
        return fileName.substring(0, index); 
    } 
 
} 
</span></pre>
<pre class="brush:java;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
public class PropertyHelper { 
     
    private ResourceBundle propBundle;  
     
    public PropertyHelper(String bundle){ 
        propBundle = PropertyResourceBundle.getBundle(bundle); 
    } 
     
    public  String getValue(String key){ 
        return this.propBundle.getString(key); 
    } 
 
} 
</pre>
<p style="padding: 0px; margin: 0px; color: rgb(44, 44, 44); font-size: 14px; font-family: Helvetica, Tahoma, Arial, sans-serif; line-height: 25px; background-color: rgb(255, 255, 255); ">
	sourceforge地址：</p>
<p style="padding: 0px; margin: 0px; color: rgb(44, 44, 44); font-size: 14px; font-family: Helvetica, Tahoma, Arial, sans-serif; line-height: 25px; background-color: rgb(255, 255, 255); ">
	<a href="https://sourceforge.net/projects/maventools/files/" style="padding: 0px; margin: 0px; color: rgb(125, 0, 0); text-decoration: none; ">https://sourceforge.net/projects/maventools/files/</a></p>

