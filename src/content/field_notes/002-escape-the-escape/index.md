---
title: "Escape the Escape"
description: "Not GO(ing) anywhere with James at the helm"
date: "Oct 10 2024"
draft: false
---
I had a tricky problem while using a Helm chart that required consuming a GO template: how could the Helm compiler stop interpolating the curly brackets? It took me longer than I'd like to admit to diagnose this issue. Below is my initial attempt at ensuring that the Helm compiler doesn't interpolate the double curly brackets.

![Escaping helm template](./002-escape-the-escape-01.png)
