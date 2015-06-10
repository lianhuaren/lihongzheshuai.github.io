---
layout: post
title: 写给我老婆的代码
date: 2012-05-29 12:15
author: onecoder
comments: true
categories: [Coding生活, 心情]
---
<pre class="brush:java;first-line:1;pad-line-numbers:true;highlight:null;collapse:false;">
package one.coder.wife;

/**
 * &lt;p&gt;
 * 这是一段Java程序写个他最爱的老婆的代码。&lt;br&gt;
 * 产生这个想法，是因为老婆要回老家几天，心里突然产生了无比依赖的感觉。&lt;br&gt;
 * 我现在只想对我的老婆说：&lt;br&gt;
 * &lt;p&gt;
 * &lt;b&gt;老婆,我爱你!&lt;/b&gt;
 * &lt;/p&gt;
 * 
 * @author lihzh(OneCoder)
 * @OneCoder-Blog http://www.coderli.com
 * @date 2012-5-25 下午9:23:11
 */
public class 给最爱的老婆 {

	// 老婆是私有，全局唯一，不可更改继承的
	private static final class 老婆 {

		// 老婆，\r 代表换行
		private static final String 漂亮等级 = &quot;世界上最美丽的\r&quot;;
		private static final String 温柔等级 = &quot;世界上最温柔的\r&quot;;
		private static final String 勤劳等级 = &quot;世界上最勤劳的\r&quot;;
		private static final String 善良等级 = &quot;世界上最善良的\r&quot;;
		private static final String 可爱等级 = &quot;世界上最可爱的\r&quot;;
		private static final String 懂我等级 = &quot;世界上最懂我的\r&quot;;
		private static final String 疼我等级 = &quot;世界上最疼我的\r&quot;;
		private static final String 照顾我等级 = &quot;世界上最精心照顾我的\r&quot;;
		private static final String 对我的意义 = &quot;你是我一生最爱的人\r&quot;;

		// 老婆是不能构造的，只能迎娶
		private 老婆() {
		}

		private static final 老婆 marryMe() {
			return new 老婆();
		}

		private boolean 笑() {
			System.out.println(&quot;老婆笑了：)&quot;);
			return true;
		}

		private boolean 哭() {
			System.out.println(&quot;呜呜，老婆伤心了。&quot;);
			return true;
		}

		private boolean 不在家() {
			System.out.println(&quot;老婆不在家。&quot;);
			return true;
		}

		private void 生气了() {
			throw new 老婆很生气Exception(&quot;老婆今天很生气，不爱理你。&quot;);
		}

		private void 破涕为笑() {
			System.out.println(&quot;老婆破涕为笑。&quot;);
		}

		@Override
		public String toString() {
			return &quot;老婆你是：\r&quot; + 漂亮等级 + 温柔等级 + 勤劳等级 + 善良等级 + 可爱等级
					+ &quot;你也是：\r&quot; + 懂我等级 + 疼我等级 + 照顾我等级 + &quot;总之，\r&quot; + 对我的意义;
		}

	}

	/*
	 * 老婆很生气异常
	 */
	private static final class 老婆很生气Exception extends RuntimeException {

		private static final long serialVersionUID = 7260098074598571319L;

		private 老婆很生气Exception(String msg) {
			super(msg);
		}
	}

	@SuppressWarnings(&quot;unused&quot;)
	public static void main(String[] args) {
		老婆 myWife = 老婆.marryMe();
		System.out.println(&quot;老婆，首先我想对你说：&quot; + myWife);

		System.out.println(&quot;如果你笑，&quot;);
		if (myWife.笑()) {
			System.out.println(&quot;我更加高兴。\r&quot;);
		}

		System.out.println(&quot;如果你哭，&quot;);
		if (myWife.哭()) {
			System.out.println(&quot;我哄你笑。\r&quot;);
		}

		if (myWife.不在家()) {
			System.out.println(&quot;我就很难入睡。\r&quot;);
		}

		System.out.println(&quot;生活中难免有琐碎、摩擦。&quot;);
		try {
			myWife.生气了();
		} catch (老婆很生气Exception e) {
			System.out.println(e.getMessage());
			System.out.println(&quot;都是我的错，是我不好。请老婆不要生气。&quot;);
		} finally {
			System.out.println(&quot;直到....&quot;);
			myWife.破涕为笑();
			// 为了空行
			System.out.println();
		}

		int myAge = 28;
		// 爱你一万年
		while (myAge &lt;= 10028) {
			boolean 我是否爱你 = true;
			myAge++;
		}
		
		System.out.println(&quot;希望能就这样平平静静的牵你手，一直走。&quot;);
		System.out.println(&quot;我爱你，我的老婆。&quot;);
		System.out.println(&quot;\t &mdash;&mdash;你的老公(苦逼coder)于：2012年5月25日晚&quot;);
	}

}

</pre>
运行后结果：<br />
&nbsp;<br />
<table border="1" cellpadding="0" cellspacing="0">
	<tbody>
		<tr>
			<td style="width:568px;">
				老婆，首先我想对你说：老婆你是：<br />
				世界上最美丽的<br />
				世界上最温柔的<br />
				世界上最勤劳的<br />
				世界上最善良的<br />
				世界上最可爱的<br />
				你也是：<br />
				世界上最懂我的<br />
				世界上最疼我的<br />
				世界上最精心照顾我的<br />
				总之，<br />
				你是我一生最爱的人<br />
				&nbsp;<br />
				如果你笑，<br />
				老婆笑了：)<br />
				我更加高兴。<br />
				&nbsp;<br />
				如果你哭，<br />
				呜呜，老婆伤心了。<br />
				我哄你笑。<br />
				&nbsp;<br />
				老婆不在家。<br />
				我就很难入睡。<br />
				&nbsp;<br />
				生活中难免有琐碎、摩擦。<br />
				老婆今天很生气，不爱理你。<br />
				都是我的错，是我不好。请老婆不要生气。<br />
				直到....<br />
				老婆破涕为笑。<br />
				&nbsp;<br />
				希望能就这样平平静静的牵你手，一直走。<br />
				我爱你，我的老婆。<br />
				&nbsp;&nbsp;&nbsp; &nbsp;&mdash;&mdash;你的老公(OneCoder)于：2012年5月25日晚<br />
				&nbsp;</td>
		</tr>
	</tbody>
</table>
&nbsp;
