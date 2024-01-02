---
title: 'Parameter-Efficient Fine-Tuning (PEFT): Prompt Tuning'
date: 2024-01-02 10:00:03 +0700
categories: [Research]
tags: [paper, nlp, ai]     
---
Bài báo chính [The Power of Scale for Parameter-Efficient Fine-tuning](https://arxiv.org/pdf/2104.08691.pdf), EMNLP, 2021.

---

Bài này sẽ giới thiệu định nghĩa về một phương pháp có ở trong PEFT là Prompt Tuning hay Soft Prompt Tuning.

## Google Translate vs. GPT

Trước khi đi cụ thể hơn vào Prompt Tuning, ta sẽ có một ví dụ so sánh giữa khả năng dịch thuật của Google Translate và GPT:

<img src="https://i.ibb.co/LdVdnPJ/Screenshot-from-2024-01-02-19-26-55.png" alt="gg" width=500/> 

<img src="https://i.ibb.co/3mBnxFL/Screenshot-from-2024-01-02-19-28-10.png" alt="gpt" width=500/>

Ở đây ta có thể thấy rằng Google Translate được xây dựng để làm một tác vụ cụ thể là dịch thuật. Trong khi đó, nếu muốn dịch thuật với GPT ta cần đưa hướng dẫn cụ thể cho GPT.

Trường hợp dưới đây sẽ thử thách hơn:

<img src="https://i.ibb.co/SRbZRqr/Screenshot-from-2024-01-02-19-29-45.png" alt="gg" width=500/> 
<img src="https://i.ibb.co/hY1w2HJ/Screenshot-from-2024-01-02-19-31-33.png" alt="gpt" width=500/>

Ta có thể thấy cả 2 đều sai trong lần đầu vì chỉ dịch word-by-word nhưng với Google Translate ta không thể nào lập tức cải thiện được chất lượng bản dịch. Còn đối với GPT ta có thể chỉ rõ hơn đây là một cụm thành ngữ để GPT đưa ra được đúng bản dịch mà chúng ta cần. 

Cách thức ta hướng dẫn cho GPT này gọi là Prompt Tuning hay chính xác hơn là Hard Prompt Tuning. 

Vậy Hard Prompt và Soft Prompt khác nhau như thế nào?


## Hard Prompt vs. Soft Prompt

<img src="https://www.researchgate.net/publication/366062946/figure/fig1/AS:11431281105340756@1670383256990/The-comparison-between-the-previous-T5-prompt-tuning-method-part-a-and-the-introduced.jpg" alt="hardprompt" width=500/>
*Source: Controlled Text Generation using T5 based Encoder-Decoder Soft Prompt Tuning and Analysis of the Utility of Generated Text in AI*

Ta thấy rằng hai thuật ngữ này khác nhau về cách ta tạo nên prompt:
- Khi tự tạo prompt bằng ngôn ngữ tự nhiên, ta nói về Hard Prompting.
- Khi prompt không phải do con người thiết kế mà được học với dữ liệu thì nó là soft promt. Cụ thể, Soft Prompt bao gồm các tham số trong không gian vector và các tham số đó có thể được điều chỉnh trong quá trình đào tạo để thu được loss nhỏ hơn và câu trả lời cho ra sẽ tốt hơn.

*Cảm ơn bạn Minh-Dat Nguyen đã đóng góp vào phần trình bày này.*
