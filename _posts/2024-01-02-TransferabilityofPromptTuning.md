---
title: 'Transferability of Prompt Tuning'
date: 2024-01-02 12:52:03 +0700
categories: [Research]
tags: [paper, nlp, ai]     
---
Tóm tắt bài báo [On Transferability of Prompt Tuning for Natural Language Processing](https://aclanthology.org/2022.naacl-main.290.pdf),  NAACL 2022.

---

## Background
- [Fine Tuning and PEFT](https://tointech.github.io/posts/FineTuning/)
- [Prompt Tuning](https://tointech.github.io/posts/SoftPrompt/)

Nhìn chung các phương pháp trong PEFT hay Prompt Tuning nói riêng sẽ giúp giảm khối lượng tính toán cho việc tinh chỉnh mô hình hơn so với việc Full Fine Tuning. 

Nhưng nếu xét về thời gian tính toán, ta sẽ nhìn vào hình bên dưới đây:

<img src="https://i.ibb.co/rQbLwt4/Screenshot-from-2024-01-02-21-04-13.png" alt="time" width=400>
*Source: On Transferability of Prompt Tuning for Natural Language Processing*

Ta thấy rằng Prompt Tuning sẽ chậm hội tụ hơn so với Fine Tuning. Vậy làm sao để cải thiện vấn đề này? 

Nhóm tác giả đề xuất rằng thay vì khởi tạo ngẫu nhiên prompt, tại sao chúng ta không tái sử dụng prompt đã được huấn luyện ở một tác vụ cùng loại để có xuất phát điểm train tốt hơn. Lúc này khái niệm Prompt Transfer được định nghĩa.

## Prompt Transfer

Nghiên cứu này tập trung vào việc liệu Prompt Transfer có thể giúp cải thiện Prompt Tuning tốt hơn không?

Nhóm tác giả cài đặt 2 thí nghiệm chính như sau:

- Cross-Task Transfer: 17 NLP Tasks in 6 Categories 
    - Sentiment Analysis: IMDB, SST-2, Laptop, Restaurant, Movie, Regionales, TweetEvel 
    - NLI: MNLI, QNLI, SNLI
    - Ethical Judgment: Deontology, Justice
    - Paraphrase Identification: QQP, MRPC
    - QA: SQuAD, NQ-Open
    - Summarization: Multi-News, SAMSum

- Cross-Model Transfer: 
    - RoBERTa-large
    - T5-XXL

### Cross-Task Transfer



### Cross-Model Transfer


<div id="disqus_thread"></div>
<script>
    /**
    *  RECOMMENDED CONFIGURATION VARIABLES: EDIT AND UNCOMMENT THE SECTION BELOW TO INSERT DYNAMIC VALUES FROM YOUR PLATFORM OR CMS.
    *  LEARN WHY DEFINING THESE VARIABLES IS IMPORTANT: https://disqus.com/admin/universalcode/#configuration-variables    */
    /*
    var disqus_config = function () {
    this.page.url = PAGE_URL;  // Replace PAGE_URL with your page's canonical URL variable
    this.page.identifier = PAGE_IDENTIFIER; // Replace PAGE_IDENTIFIER with your page's unique identifier variable
    };
    */
    (function() { // DON'T EDIT BELOW THIS LINE
    var d = document, s = d.createElement('script');
    s.src = 'https://https-tointech-github-io.disqus.com/embed.js';
    s.setAttribute('data-timestamp', +new Date());
    (d.head || d.body).appendChild(s);
    })();
</script>
<noscript>Please enable JavaScript to view the <a href="https://disqus.com/?ref_noscript">comments powered by Disqus.</a></noscript>