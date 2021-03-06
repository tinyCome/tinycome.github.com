---
layout : post
title : 适配器模式
tagline : Supporting tagline
tags : [php,设计模式]
category : php
---
{% include JB/setup %}


--参考书籍《php设计模式》<br/>
当外部API库改变时，如何让使用者动态的升级？<br/>
运用适配器(Adapter)模式来避免因外部库改变所带来的不便。<br/>

如向上兼容举例：<br/>
使用者版本1-hello    ------>  最新库的-haoo（库版本1时，是hello方法） <br/>
使用者版本2-greet    ------> 最新库的-haoo（库版本2时，hello方法消失，变成greet方法）<br/>
使用者版本3-haoo    ------> 最新库的-haoo（版本库3时，greet方法也消失，变成haoo方法）<br/>


	<?php
	//version1
	/*
	class HwLib {
	 function hello(){
		 return 'Hello ';
	 } 
	 function world() {
		 return 'World!';
	 }
	}
	$s = new HwLib;
	echo $s->hello(),$s->world();
	*/
	// version 2
	/*
	class HwLib {
	 function greet(){
		 return 'Greetings and Salutations ';
	 }
	 function world() {
		 return 'World!'."\n";
	 }
	}
	*/
	// version 3
	class HwLib {
	 function haoo(){
		 return 'haoo++++++++ ';
	 }
	 function world() {
		 return 'World!'."\n";
	 }
	}
	class HwLibV2ToV1Adapter{
	 var$libv2;
	 function HwLibV2ToV1Adapter(&$libv2) {
		 $this->libv2 = &$libv2;
	 }
	 function hello(){
		 return $this->libv2->greet();
	 }
	 function world() {
		 return $this->libv2->world();
	 } 
	}
	class HwLibV3ToV2Adapter{
	 var$libv3;
	 function HwLibV3ToV2Adapter(&$libv3) {
		 $this->libv3 = &$libv3;
	 }
	 function greet(){
		 return $this->libv3->haoo();
	 }
	 function world() {
		 return $this->libv3->world();
	 } 
	}
	function & HwLibInstance($ver='') {
	 switch ($ver) {
	 case 'V3':
		 return new HwLib;
	 case 'V2':
		 return new HwLibV3ToV2Adapter(new HwLib); 
	 default:
		 return new HwLibV2ToV1Adapter(new HwLibV3ToV2Adapter(new HwLib));
	 }
	} 
	$s = HwLibInstance('V1');
	echo $s->hello(),$s->world();
	$s = HwLibInstance('V2');
	echo $s->greet(),$s->world();
	$s = HwLibInstance('V3');
	echo $s->haoo(),$s->world();
	class HwLibGofV2ToV1Adapter extends HwLib{ 
	 function hello(){
		 return parent::greet();
	 } 
	}
	class HwLibGofV3ToV2Adapter extends HwLibGofV2ToV1Adapter{
	 function hello(){
		 return parent::haoo();
	 } 
	 function greet(){
	 //return parent::haoo();
		 return $this->haoo();
	 }
	}
	function & HwLibInstanceGof($ver='') { 
		 return new HwLibGofV3ToV2Adapter;
	}
	$s = HwLibInstanceGof('V1');
	echo $s->hello(),$s->world();
	$s = HwLibInstanceGof('V2');
	echo $s->greet(),$s->world();
	$s = HwLibInstanceGof('V3');
	echo $s->haoo(),$s->world();


