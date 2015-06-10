---
layout: post
title: 让JTextField添加类似Google的“自动补全”功能
date: 2012-06-13 21:51
author: onecoder
comments: true
categories: [jtextfield, Swing, swing, 自动填充]
---
<p>
	在越来越重视&ldquo;用户体验&rdquo;的今天，一个简单的文本框也演进的越来越智能了。比如Google的搜索，当我们输入搜索关键字的过程中，文本框就会动态的下拉列出最常输入的近似文字，以便我们快速输入要查询的内容。当然一直抄袭Google的百度自然也是一样。类似的例子还有很多，例如一般的邮件客户端，在敲入地址时，也会动态列出符合要求的地址，方便快速录入，也会减少出错。 <a href="http://www.coderli.com/?attachment_id=4366" rel="attachment wp-att-4366"><img alt="" class="aligncenter" height="500" src="http://twaver.servasoft.com/wp-content/uploads/2012/06/2bizbox-5-644x500.png" title="2bizbox-5" width="644" /></a>&nbsp;<a href="http://www.coderli.com/?attachment_id=4367" rel="attachment wp-att-4367"><img alt="" class="aligncenter" height="121" src="http://twaver.servasoft.com/wp-content/uploads/2012/06/2bizbox-6-644x121.png" title="2bizbox-6" width="644" /></a> 那么，Swing的文本框要做到这一点是否容易呢？网上的例子也能搜索到一些，不过要么功能做的太简单，要么实现的代码太繁琐罗嗦。还有一些商业的Swing组件，则完全是要付费的。本文结合了2BizBox免费ERP软件开发中的实践，尝试了一种非常简单、有效的方法来制作这一效果。</p>
<div>
	<a href="http://www.coderli.com/?attachment_id=4371" rel="attachment wp-att-4371"><img alt="" class="aligncenter" height="249" src="http://twaver.servasoft.com/wp-content/uploads/2012/06/2bizbox-10.png" title="2bizbox-10" width="405" /></a></div>
<p>
	首先仔细观察这种效果：它外观上、本质上，都完全是一个文本框，而不是下拉框。所以，我们不想把它做成下拉框，也就是不想从JComboBox继承。另外，下拉列表提示的出现，是完全异步、动态的，它仅仅作为提示，不能干预正常的文本框的输入。最后，那个下拉列表的外观和行为则完全是一个JComboBox的下拉列表行为。所以，这个&ldquo;可自动完成的JTextField&rdquo;应当是一个JTextField和JComboBox下拉列表部分的结合体。 经过以上分析，思路基本确定：它本质是一个JTextField，但是又结合利用了一个JComboBox的下拉列表。二者合而为一即可。那么是从谁继承呢？JTextField吗？ 仔细想想，继承并不是最好的方法。俗话说：继承是混蛋。能不继承就不要继承。为啥呢？继承，意味着别人只能继承你的类，才能使用这一功能。假如你的项目已经写了一万多个界面，想给这里面的一些文本框增加这种智能提示功能，难道要对所有代码进行修改，让那些东西重新继承你的类吗？这无疑是个烂主意。所以，那些刚学会OO的童鞋，总是喜欢动不动就要继承的思路，并不妥当。如果我们只是提供一个Util方法，对已经存在的普通JTextField实例处理一下，就可以具有智能提示，岂不是更好？ 要做到JTextField和JComboBox这两个组件的结合，这里使用了非常&ldquo;怪异&rdquo;的一个绝招，你绝对想不到：把一个JComboBox塞到JTextField的身体里面，并让它看不见。看一下代码： JTextField txtInput = new JTextField();</p>
<pre class="brush:java;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
JComboBox cbInput = new JComboBox(); 
txtInput.setLayout(new BorderLayout()); txtInput.add(cbInput, BorderLayout.SOUTH); 
</pre>
<div>
	<div>
		什么？把JTextField设置一个layout？并且还add一个JComboBox且放在SOUTH？我相信你绝对闻所未闻这种事情。怎么看都是怪胎啊。不要紧，把JComboBox的高度变成0，别人就看不出破绽了：</div>
	<div>
		<pre class="brush:java;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
 JComboBox cbInput = new JComboBox(model) { 
public Dimension getPreferredSize() { 
return new Dimension(super.getPreferredSize().width, 0); 
} }; 
</pre>
	</div>
	<div>
		虽然combo看不见，但是它实实在在存在于文本框的身体里，且位于其下方。我们的思路是：当文本框输入内容时，我们判断下拉框中是否有符合要求的列表，如果有，就马上主动弹出下拉；否则就让下拉消失。 监控文本框输入并不难：给它的document增加listener就行了。这里我们使用了&ldquo;不区分大小写&rdquo;、&ldquo;和输入字符串开头相同的项&rdquo;的规则进行过滤。将所有备选字符串置于单独一个数组中，每次用户输入后，动态过滤出符合条件的字符串，动态添加到JComboBox中，并将其下拉列表Popup出来即可：&nbsp;</div>
	<pre class="brush:java;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
txtInput.getDocument().addDocumentListener(new DocumentListener() { public void insertUpdate(DocumentEvent e) { updateList(); }
 public void removeUpdate(DocumentEvent e) { updateList(); }
 public void changedUpdate(DocumentEvent e) { updateList(); } 
private void updateList() { setAdjusting(cbInput, true); 
model.removeAllElements(); 
String input = txtInput.getText(); 
if (!input.isEmpty()) { 
for (String item : items) { 
if (item.toLowerCase().startsWith(input.toLowerCase())) { 
model.addElement(item); } } } cbInput.setPopupVisible(model.getSize() &gt; 0); 
setAdjusting(cbInput, false); } }); 
</pre>
</div>
<p>
	此外，为了更方便操作，我们再增加几个快捷键：当输入ESC，主动关掉下拉列表；当输入回车或空格，直接把第一项符合要求的字符串输入文本框：</p>
<p>
	&nbsp;</p>
<pre class="brush:java;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
txtInput.addKeyListener(new KeyAdapter() {

@Override
public void keyPressed(KeyEvent e) {
setAdjusting(cbInput, true);
if (e.getKeyCode() == KeyEvent.VK_SPACE) {
if (cbInput.isPopupVisible()) {
e.setKeyCode(KeyEvent.VK_ENTER);
}
}
if (e.getKeyCode() == KeyEvent.VK_ENTER || e.getKeyCode() == KeyEvent.VK_UP || e.getKeyCode() == KeyEvent.VK_DOWN) {
e.setSource(cbInput);
cbInput.dispatchEvent(e);
if (e.getKeyCode() == KeyEvent.VK_ENTER) {
txtInput.setText(cbInput.getSelectedItem().toString());
cbInput.setPopupVisible(false);
}
}
if (e.getKeyCode() == KeyEvent.VK_ESCAPE) {
cbInput.setPopupVisible(false);
}
setAdjusting(cbInput, false);
}
});
</pre>
<p>
	&nbsp;还有一个非常重要的技术要点要进行说明。在popup列表弹出的时候，我们希望用箭头能够上下移动选择条目，但是又同时希望当前的光标和焦点不要离开文本框。这个好像非常难实现啊！请看我们是如何做到的：在监控到上下箭头输入时候，把当前的键盘事件的source动态修改为JComboBox，然后派发给JComboBox。也就是说，本来事件是输入到文本框的，我们把邮递员拦截下来，把收件人改一下，继续交给邮递员进行派发。这样，就做到&ldquo;移花接木&rdquo;了：</p>
<p>
	&nbsp;</p>
<pre class="brush:java;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
if (e.getKeyCode() == KeyEvent.VK_ENTER || e.getKeyCode() == KeyEvent.VK_UP || e.getKeyCode() == KeyEvent.VK_DOWN) {
e.setSource(cbInput);
cbInput.dispatchEvent(e);
if (e.getKeyCode() == KeyEvent.VK_ENTER) {
txtInput.setText(cbInput.getSelectedItem().toString());
cbInput.setPopupVisible(false);
}
}
</pre>
<p>
	最后，为了演示效果，我们放一些数据到下拉列表中。放什么呢？自己造假数据太麻烦了，干脆用Java中的&ldquo;所有国家&rdquo;的数据吧，简单省事：</p>
<p>
	&nbsp;</p>
<pre class="brush:java;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
Locale[] locales = Locale.getAvailableLocales();
for (int i = 0; i &amp;lt; locales.length; i++) {
String item = locales[i].getDisplayName();
items.add(item);
}
</pre>
<p>
	最后看一下效果，完全符合我们的预期：</p>
<div>
	<a href="http://www.coderli.com/?attachment_id=4365" rel="attachment wp-att-4365"><img alt="" height="400" src="http://twaver.servasoft.com/wp-content/uploads/2012/06/2bizbox-8.png" title="2bizbox-8" width="500" /></a></div>
<p>
	以下是完整代码：</p>
<p>
	&nbsp;</p>
<pre class="brush:java;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
import java.awt.*;
import java.awt.event.*;
import java.util.*;

import javax.swing.*;
import javax.swing.event.*;

import twaver.*;

public class Test {

public static void main(String[] args) throws Exception {
UIManager.setLookAndFeel(UIManager.getSystemLookAndFeelClassName());
JFrame frame = new JFrame();
frame.setTitle(&quot;Auto Completion Test&quot;);
frame.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
frame.setBounds(200, 200, 500, 400);

ArrayList&amp;lt;String&amp;gt; items = new ArrayList&amp;lt;String&amp;gt;();
Locale[] locales = Locale.getAvailableLocales();
for (int i = 0; i &amp;lt; locales.length; i++) {
String item = locales[i].getDisplayName();
items.add(item);
}
JTextField txtInput = new JTextField();
setupAutoComplete(txtInput, items);
txtInput.setColumns(30);
frame.getContentPane().setLayout(new FlowLayout());
frame.getContentPane().add(txtInput, BorderLayout.NORTH);
frame.setVisible(true);
}

private static boolean isAdjusting(JComboBox cbInput) {
if (cbInput.getClientProperty(&quot;is_adjusting&quot;) instanceof Boolean) {
return (Boolean) cbInput.getClientProperty(&quot;is_adjusting&quot;);
}
return false;
}

private static void setAdjusting(JComboBox cbInput, boolean adjusting) {
cbInput.putClientProperty(&quot;is_adjusting&quot;, adjusting);
}

public static void setupAutoComplete(final JTextField txtInput, final ArrayList&amp;lt;String&amp;gt; items)          final DefaultComboBoxModel model = new DefaultComboBoxModel();
final JComboBox cbInput = new JComboBox(model) {
public Dimension getPreferredSize() {
return new Dimension(super.getPreferredSize().width, 0);
}
};
setAdjusting(cbInput, false);
for (String item : items) {
model.addElement(item);
}
cbInput.setSelectedItem(null);
cbInput.addActionListener(new ActionListener() {
@Override
public void actionPerformed(ActionEvent e) {
if (!isAdjusting(cbInput)) {
if (cbInput.getSelectedItem() != null) {
txtInput.setText(cbInput.getSelectedItem().toString());
}
}
}
});

txtInput.addKeyListener(new KeyAdapter() {

@Override
public void keyPressed(KeyEvent e) {
setAdjusting(cbInput, true);
if (e.getKeyCode() == KeyEvent.VK_SPACE) {
if (cbInput.isPopupVisible()) {
e.setKeyCode(KeyEvent.VK_ENTER);
}
}
if (e.getKeyCode() == KeyEvent.VK_ENTER || e.getKeyCode() == KeyEvent.VK_UP || e.getKeyCode() == KeyEvent.VK_DOWN) {
e.setSource(cbInput);
cbInput.dispatchEvent(e);
if (e.getKeyCode() == KeyEvent.VK_ENTER) {
txtInput.setText(cbInput.getSelectedItem().toString());
cbInput.setPopupVisible(false);
}
}
if (e.getKeyCode() == KeyEvent.VK_ESCAPE) {
cbInput.setPopupVisible(false);
}
setAdjusting(cbInput, false);
}
});
txtInput.getDocument().addDocumentListener(new DocumentListener() {
public void insertUpdate(DocumentEvent e) {
updateList();
}

public void removeUpdate(DocumentEvent e) {
updateList();
}

public void changedUpdate(DocumentEvent e) {
updateList();
}

private void updateList() {
setAdjusting(cbInput, true);
model.removeAllElements();
String input = txtInput.getText();
if (!input.isEmpty()) {
for (String item : items) {
if (item.toLowerCase().startsWith(input.toLowerCase())) {
model.addElement(item);
}
}
}
cbInput.setPopupVisible(model.getSize() &amp;gt; 0);
setAdjusting(cbInput, false);
}
});
txtInput.setLayout(new BorderLayout());
txtInput.add(cbInput, BorderLayout.SOUTH);
}
}
</pre>
<p>
	<cite>转自：<a href="http://www.blogjava.net/TWaver/archive/2012/06/12/380602.html">http://www.blogjava.net/TWaver/archive/2012/06/12/380602.html</a></cite> <span style="color: #008080;"> </span></p>

