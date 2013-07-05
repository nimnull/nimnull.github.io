---
layout: post
title: Chrome extensions и закрытия окна (диалога) расширения
---

<div dir="ltr" style="text-align: left;" trbidi="on">
<style type="text/css">
.javascript .de1, .javascript .de2 {font: normal normal monospace; margin:0; padding:0; background:none; vertical-align:top;color: #000020;}
.javascript  {font-family:monospace;color: #006; border: 1px solid #d0d0d0; background-color: #f0f0f0;}
.javascript a:link {color: #000060;}
.javascript a:hover {background-color: #f0f000;}
.javascript .imp {font-weight: bold; color: red;}
.javascript li, .javascript .li1 {font-weight: normal; vertical-align:top;font: normal normal 'Courier New', Courier, monospace; color: #003030;}
.javascript .ln {width:1px;text-align:right;margin:0;padding:0 2px;vertical-align:top;}
.javascript .li2 {font-weight: bold; vertical-align:top;font-weight: bold; color: #006060;}
.javascript .kw1 {color: #000066; font-weight: bold;}
.javascript .kw2 {color: #003366; font-weight: bold;}
.javascript .kw3 {color: #000066;}
.javascript .co1 {color: #006600; font-style: italic;}
.javascript .co2 {color: #009966; font-style: italic;}
.javascript .coMULTI {color: #006600; font-style: italic;}
.javascript .es0 {color: #000099; font-weight: bold;}
.javascript .br0 {color: #009900;}
.javascript .sy0 {color: #339933;}
.javascript .st0 {color: #3366CC;}
.javascript .nu0 {color: #CC0000;}
.javascript .me1 {color: #660066;}
.javascript span.xtra { display:block; }
</style>
<br />
<div dir="ltr" style="text-align: left;" trbidi="on">
С недавних пор мне выпал случай создать расширения для Google Chrome. И тут появился ряд нетривиальных задач, которые пришлось решить в процессе. Хочу поделиться небольшим опытом, который я приобрел в процессе:<br />
<!--more--><br />
1. При закрытии popup окошка, вызванного нажатием на значок расширения в панели Chrome мне потребовалось выполнять кое-какие действия. Логично повесить <code>handler</code> на событие <code>onbeforeunload</code> или <code>unload</code> объекта текущего <code>window</code>. Так то оно так, но не совсем. Обычные действия, внутри <code>callback</code> этого события отказываются выполнятся наотрез. Но! Работает взаимодействие с <code>localStorage</code>. Путем нехитрых умозаключений и поиска похожей проблемы на просторах интернетов появилось такое вот решение:</div>
<div class="javascript">
<ol>
<li class="li1"><div class="de1">
<span class="kw2">var</span> background <span class="sy0">=</span> chrome.<span class="me1">extension</span>.<span class="me1">getBackgroundPage</span><span class="br0">(</span><span class="br0">)</span><span class="sy0">;</span></div>
</li>
<li class="li1"><div class="de1">
window.<span class="me1">addEventListener</span><span class="br0">(</span><span class="st0">'unload'</span><span class="sy0">,</span> <span class="kw2">function</span><span class="br0">(</span><span class="br0">)</span> <span class="br0">{</span></div>
</li>
<li class="li1"><div class="de1">
&nbsp; background.<span class="me1">indicator</span>.<span class="me1">show</span><span class="br0">(</span><span class="br0">)</span><span class="sy0">;</span></div>
</li>
<li class="li1"><div class="de1">
<span class="br0">}</span><span class="sy0">,</span> <span class="kw2">true</span><span class="br0">)</span><span class="sy0">;</span></div>
</li>
</ol>
</div>
Суть метода заключается в описании действия в <code>background</code> странице и последующем вызове этого действия при наступлении события через объект <code>backgroundPage</code>. Связано это с тем, что <code>backgroundPage</code> расширения живет еще какое-то время после закрытия всплывающего окна расширения (около 500мс), что дает небольшое окно времени для моих целей. Код подключаемый к background.html выглядит так:
<br />
<div class="javascript">
<ol>
<li class="li1"><div class="de1">
<span class="kw2">function</span> Indicator<span class="br0">(</span><span class="br0">)</span> <span class="br0">{</span> <span class="br0">}</span></div>
</li>
<li class="li1"><div class="de1">
Indicator.<span class="me1">prototype</span>.<span class="me1">show</span> <span class="sy0">=</span> <span class="kw2">function</span><span class="br0">(</span><span class="br0">)</span> <span class="br0">{</span></div>
</li>
<li class="li1"><div class="de1">
&nbsp; &nbsp; <span class="kw1">if</span> <span class="br0">(</span>localStorage.<span class="me1">unsubmitedCount</span><span class="br0">)</span> <span class="br0">{</span></div>
</li>
<li class="li1"><div class="de1">
&nbsp; &nbsp; &nbsp; &nbsp; chrome.<span class="me1">browserAction</span>.<span class="me1">setBadgeText</span><span class="br0">(</span><span class="br0">{</span>text<span class="sy0">:</span> localStorage.<span class="me1">unsubmitedCount</span><span class="br0">}</span><span class="br0">)</span><span class="sy0">;</span></div>
</li>
<li class="li2"><div class="de2">
&nbsp; &nbsp; &nbsp; &nbsp; chrome.<span class="me1">browserAction</span>.<span class="me1">setTitle</span><span class="br0">(</span><span class="br0">{</span>title<span class="sy0">:</span> <span class="st0">"You have some actions to save"</span><span class="br0">}</span><span class="br0">)</span><span class="sy0">;</span></div>
</li>
<li class="li1"><div class="de1">
&nbsp; &nbsp; <span class="br0">}</span></div>
</li>
<li class="li1"><div class="de1">
<span class="br0">}</span></div>
</li>
<li class="li1"><div class="de1">
Indicator.<span class="me1">prototype</span>.<span class="me1">update</span> <span class="sy0">=</span> <span class="kw2">function</span><span class="br0">(</span><span class="br0">)</span> <span class="br0">{</span></div>
</li>
<li class="li1"><div class="de1">
&nbsp; &nbsp; <span class="kw1">if</span><span class="br0">(</span><span class="sy0">!</span>localStorage.<span class="me1">unsubmitedCount</span><span class="br0">)</span> <span class="br0">{</span></div>
</li>
<li class="li2"><div class="de2">
&nbsp; &nbsp; &nbsp; &nbsp; localStorage.<span class="me1">unsubmitedCount</span> <span class="sy0">=</span> <span class="nu0">1</span><span class="sy0">;</span></div>
</li>
<li class="li1"><div class="de1">
&nbsp; &nbsp; <span class="br0">}</span> <span class="kw1">else</span> <span class="br0">{</span></div>
</li>
<li class="li1"><div class="de1">
&nbsp; &nbsp; &nbsp; &nbsp; <span class="sy0">++</span>localStorage.<span class="me1">unsubmitedCount</span><span class="sy0">;</span></div>
</li>
<li class="li1"><div class="de1">
&nbsp; &nbsp; <span class="br0">}</span></div>
</li>
<li class="li1"><div class="de1">
<span class="br0">}</span></div>
</li>
<li class="li2"><div class="de2">
Indicator.<span class="me1">prototype</span>.<span class="me1">reset</span> <span class="sy0">=</span> <span class="kw2">function</span><span class="br0">(</span><span class="br0">)</span> <span class="br0">{</span></div>
</li>
<li class="li1"><div class="de1">
&nbsp; &nbsp; chrome.<span class="me1">browserAction</span>.<span class="me1">setBadgeText</span><span class="br0">(</span><span class="br0">{</span>text<span class="sy0">:</span> <span class="st0">""</span><span class="br0">}</span><span class="br0">)</span><span class="sy0">;</span></div>
</li>
<li class="li1"><div class="de1">
&nbsp; &nbsp; chrome.<span class="me1">browserAction</span>.<span class="me1">setTitle</span><span class="br0">(</span><span class="br0">{</span>title<span class="sy0">:</span> <span class="st0">"Default extension text"</span><span class="br0">}</span><span class="br0">)</span><span class="sy0">;</span></div>
</li>
<li class="li1"><div class="de1">
&nbsp; &nbsp; localStorage.<span class="me1">removeItem</span><span class="br0">(</span><span class="st0">'unsubmitedCount'</span><span class="br0">)</span><span class="sy0">;</span></div>
</li>
<li class="li1"><div class="de1">
<span class="br0">}</span></div>
</li>
<li class="li2"><div class="de2">
</div>
</li>
<li class="li1"><div class="de1">
<span class="kw2">var</span> indicator <span class="sy0">=</span> <span class="kw2">new</span> Indicator<span class="br0">(</span><span class="br0">)</span><span class="sy0">;</span></div>
</li>
<li class="li1"><div class="de1">
</div>
</li>
<li class="li1"><div class="de1">
</div>
</li>
</ol>
</div>
</div>
