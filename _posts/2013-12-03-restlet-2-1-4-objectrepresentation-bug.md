---
layout: post
title: Restlet 2.1.4中 匪夷所思的ObjectRepresentation的构造函数
date: 2013-12-03 22:26
author: onecoder
comments: true
categories: [Java, objectrepresentation, restlet, restlet]
---
<p>
	<a href="http://www.coderli.com">OneCoder</a>使用Restlet最新版2.1.4开发样例，却一直抛出异常：</p>
<blockquote>
	<p>
		Exception in thread &quot;main&quot; java.lang.IllegalArgumentException : The serialized representation must have this media type: application/x-java-serialized-object or this one: application/x-java-serialized-object+xml</p>
	<p>
		&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; at org.restlet.representation.ObjectRepresentation.&lt;init&gt;(ObjectRepresentation.java:203)<br />
		&nbsp;&nbsp;&nbsp;&nbsp; at org.restlet.representation.ObjectRepresentation.&lt;init&gt;(ObjectRepresentation.java:114)</p>
</blockquote>
<p>
	无论怎么设置MediaType都无效，无奈只能查看次构造函数的源码：</p>
<pre class="brush:java;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
public ObjectRepresentation(Representation serializedRepresentation,
            final ClassLoader classLoader) throws IOException,
            ClassNotFoundException, IllegalArgumentException {
        super(MediaType.APPLICATION_JAVA_OBJECT);


        if (serializedRepresentation.getMediaType().equals(
                MediaType. APPLICATION_JAVA_OBJECT)) {
            if (!VARIANT_OBJECT_BINARY_SUPPORTED ) {
                throw new IllegalArgumentException(
                        &quot;SECURITY WARNING: The usage of ObjectInputStream when &quot;
                                + &quot;deserializing binary presentations from unstrusted &quot;
                                + &quot;sources can lead to malicious attacks. As pointed &quot;
                                + &quot;here (https://github.com/restlet/restlet-framework-java/issues/778), &quot;
                                + &quot;the ObjectInputStream class is able to force the JVM to execute unwanted &quot;
                                + &quot;Java code. Thus, the support of such format has been disactivated &quot;
                                + &quot;by default. You can activate this support by turning on the following system property: &quot;
                                + &quot;org.restlet.representation.ObjectRepresentation.VARIANT_OBJECT_BINARY_SUPPORTED.&quot; );
            }
            setMediaType(MediaType.APPLICATION_JAVA_OBJECT );
            InputStream is = serializedRepresentation.getStream();
            ObjectInputStream ois = null;
            if (classLoader != null) {
                ois = new ObjectInputStream(is) {
                    @Override
                    protected Class&lt;?&gt; resolveClass(
                            java.io.ObjectStreamClass desc)
                            throws java.io.IOException,
                            java.lang.ClassNotFoundException {
                        return Class
                                . forName(desc.getName(), false, classLoader);
                    }
                };
            } else {
                ois = new ObjectInputStream(is);
            }


            this.object = (T) ois.readObject();


            if (is.read() != -1) {
                throw new IOException(
                        &quot;The input stream has not been fully read.&quot;);
            }


            ois.close();
        } else if (VARIANT_OBJECT_XML_SUPPORTED
                &amp;&amp; serializedRepresentation.getMediaType().equals(
                        MediaType.APPLICATION_JAVA_OBJECT_XML )) {
            if (!VARIANT_OBJECT_XML_SUPPORTED ) {
                throw new IllegalArgumentException(
                        &quot;SECURITY WARNING: The usage of XMLDecoder when &quot;
                                + &quot;deserializing XML presentations from unstrusted &quot;
                                + &quot;sources can lead to malicious attacks. As pointed &quot;
                                + &quot;here (http://blog.diniscruz.com/2013/08/using-xmldecoder-to-execute-server-side.html), &quot;
                                + &quot;the XMLDecoder class is able to force the JVM to &quot;
                                + &quot;execute unwanted Java code described inside the XML &quot;
                                + &quot;file. Thus, the support of such format has been &quot;
                                + &quot;disactivated by default. You can activate this &quot;
                                + &quot;support by turning on the following system property: &quot;
                                + &quot;org.restlet.representation.ObjectRepresentation.VARIANT_OBJECT_XML_SUPPORTED.&quot; );
            }
            setMediaType(MediaType.APPLICATION_JAVA_OBJECT_XML );
            InputStream is = serializedRepresentation.getStream();
            java.beans.XMLDecoder decoder = new java.beans.XMLDecoder(is);
            this.object = (T) decoder.readObject();


            if (is.read() != -1) {
                throw new IOException(
                        &quot;The input stream has not been fully read.&quot;);
            }


            decoder.close();
        }
        throw new IllegalArgumentException(
                &quot;The serialized representation must have this media type: &quot;
                        + MediaType.APPLICATION_JAVA_OBJECT .toString()
                        + &quot; or this one: &quot;
                        + MediaType.APPLICATION_JAVA_OBJECT_XML .toString());
    }

</pre>
<p>
	惊呆的发现，最后的throw new IllegalArgumentException 逻辑被赤裸裸的暴露在外面，也就是不论上面走的是if还是else，最终都会走到这里抛出异常结束。这不免让我一头雾水，回头查看2.1.2版本的源码，发现抛出异常的代码是写在最后的else块里的，这可就大不相同了。</p>
<p>
	我只能以我目前粗浅的了解，怀疑这是restlet的一个粗心的bug，我已经给restlet发了邮件咨询了该问题，等待回复中。目前，我也只能降到2.1.2版本的restlet进行开发，在2.1.2版上无此问题。</p>

