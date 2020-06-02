---
layout: single
title: Approximating the value of Pi
date:   2020-06-02 22:00:09 +0900
categories: jekyll update
author : Sang Ji
---


~~~java
float r = 200;

int total = 0;
int circle = 0;

int recordPI = 0;

int resultP;

void setup() {
	size(402, 402);
	background(0);
	translate(width / 2, height / 2);
	stroke(255);
	strokeWeight(4);
    noFill();
	ellipse(0, 0, r * 2, r * 2);
	rectMode(CENTER);
	rect(0, 0, r * 2, r * 2);
		  
	float x = random(-r, r);
	float x = random(-r, r);
		  
	float d = sqrt(x*x + y*y);
	if (d < r) {
		 stroke(100,255,0);
		  } else {
		 stroke(0,100,255);
		  }
		  
	 float pi = 4 * (circle / total);
		  println(pi);
		  point(x, y);
		}

void draw() {
	translate(width / 2, height / 2);
    for (int i = 0; i < 10000; i++) {
		 float x = random(-r, r);
		 float y = random(-r, r);
		 total++;

        double d = (double)x * (double)x + (double)y * (double)y;
		if (d < (double)r * (double)r) {
				  circle++;
				  stroke(100, 255, 0, 100);
			  } else {
				  stroke(0, 100, 255, 100);
			  }
        strokeWeight(1);
		point(x, y);

        double pi = (double)4 * ((double)circle / (double)total);
		double recordDiff = Math.abs(Math.PI - recordPI);
		double diff = Math.abs(Math.PI - pi);
        if (diff < recordDiff) {
            recordDiff = diff;
		    recordPI = pi;
		    println(recordPI);
~~~

반지름이 r인 원과 그 원을 포함하는 지름이 2r인 사각형에 무작위로 다트를 던졌을 때 전체 사각형 안의 다트의 와 원 안에만 존재하는 다트의 비율을 통해서 파이값을 유도 할 수 있고 다트의 수가 많아질수록 파이값에 유사해진다.

구하는 식은 4 * (원 안의 다트의 수) / (전체 사각형 안의 다트의 수) 이다.