---
title: "Sign2GPT: Leveraging Large Language Models for Gloss-Free Sign Language Translation"
collection: publications
category: conferences
permalink: /publication/sign2gpt
# excerpt:  'This paper is about the number 1. The number 2 is left for future work.'
date: 2024-06-01
venue: 'ICLR'
# slidesurl: 'http://academicpages.github.io/files/slides1.pdf'
paperurl: 'https://openreview.net/pdf?id=LqaEEs3UxU'
citation: '<strong>Ryan Wong</strong>, Necati Cihan Camgoz, Richard Bowden'
---

Automatic Sign Language Translation requires the integration of both computer vision and natural language processing to effectively bridge the communication gap between sign and spoken languages. However, the deficiency in large-scale training data to support sign language translation means we need to leverage resources from spoken language. We introduce, Sign2GPT, a novel framework for sign language translation that utilizes large-scale pretrained vision and language models via lightweight adapters for gloss-free sign language translation. The lightweight adapters are crucial for sign language translation, due to the constraints imposed by limited dataset sizes and the computational requirements when training with long sign videos. We also propose a novel pretraining strategy that directs our encoder to learn sign representations from automatically extracted pseudo-glosses without requiring gloss order information or annotations. We evaluate our approach on two public benchmark sign language translation datasets, namely RWTH-PHOENIX-Weather 2014T and CSL-Daily, and improve on state-of-the-art gloss-free translation performance with a significant margin.