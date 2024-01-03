---
title: 'Transferability of Prompt Tuning'
date: 2024-01-03 12:52:03 +0700
categories: [Research]
tags: [paper, nlp, ai]   
math: true  
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

Nhóm tác giả đề xuất rằng thay vì khởi tạo ngẫu nhiên prompt, tại sao chúng ta không tái sử dụng prompt đã được huấn luyện ở một tác vụ cùng loại để có xuất phát điểm tốt hơn. Lúc này khái niệm Prompt Transfer được định nghĩa.

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

- Mục tiêu: Kiểm tra tính hiệu quả và hiệu suất Prompt Tuning kèm với việc chuyển giao câu prompt trong các tác vụ khác nhau.

- Cách thức thực hiện: Ta sẽ thực hiện huấn luyện prompt ở tác vụ nguồn (Source task) sau đó sử dụng trực tiếp prompt cho tác vụ đích (Target task) như hình minh họa bên dưới đây:

<img src="https://i.ibb.co/n8XqNPV/Screenshot-from-2024-01-03-13-22-35.png" alt="crosstask">
*Source: On Transferability of Prompt Tuning for Natural Language Processing*

- Cấu trúc câu input của promt: `<soft><mask><text_a><text_b>`
  - Ví dụ: Tác vụ Suy luận ngôn ngữ tự nhiên (NLI)
    - Ta cho trước 1 cặp câu A và B và muốn xác định mối quan hệ giữa A và B (dẫn xuất - entailment, mâu thuẫn- contradiction,hoặc trung lập - neutral)
      - `soft`: Does the first sentence entails the second?
      - `mask`
      - `text_a`: A soccer game with multiple males playing.
      - `text_b`: Some men are playing a sport.

- Thử nghiệm thực tế:
  - Backbone: RoBeRTa-base
  - Task type: Sentiment analysis
  - Source Task : 
    - Dataset: SST2
    - Evaluate accuracy: 77.17%
  - Target Task:
    - Dataset: rotten_tomatoes
    - Evaluate accuracy: 72.13%

Đầu tiên ta thực hiện quá trình Prompt Tuning trên tập nguồn là SST2 với thể loại tác vụ là Phân tích cảm xúc (Sentiment analysis). Sau đó ta chuyển giao toàn bộ prompt cho tác vụ đích có cùng thể loại là rotten_tomatoes mà không tinh chỉnh gì thêm (Zero-shot transfer). Độ chính xác đánh giá được lúc này đã là 72.13%. Trong khi đó, nếu ta Prompt Tuning tác vụ đích từ đầu thì kết quả thu được là 74.10%. Có thể thấy rằng việc chuyển giao prompt lúc này sẽ mang lại khởi đầu cực kì tốt để quá trình huấn luyện sau này được tiết kiệm thời gian hơn.

<img src="https://i.ibb.co/n1fnmjH/Screenshot-from-2024-01-03-14-11-34.png" alt="heatmap" />

Biểu đồ heatmap cho ta thấy được mức độ tương quan giữa tập nguồn và tập đích khi thực hiện việc chuyển giao prompt. Nhìn chung, Prompt Transfer sẽ cho hiệu suất sẽ tương tự hoặc tốt hơn so với khởi tạo ngẫu nhiên và tốn ít thời gian huấn luyện hơn cho các tác vụ tương tự nhau.

### Cross-Model Transfer
- Mục tiêu: Nghiên cứu khả năng để chuyển giao Soft Prompt giữa Pre-trained language model (PLM) nhỏ đến PLM lớn. Cụ thể ở đây ta thử nghiệm trên:
  - Source model: RoBERTa (small PLM)
  - Target model: T5 (large PLM) 

Huấn luyện prompt trên mô hình nhỏ giúp tiết kiệm thời gian và chi phí tính toán hơn so với huấn luyện trên mô hình lớn hơn.

- Cách thức thực hiện:
  
<img src="https://i.ibb.co/QDq74Y4/Screenshot-from-2024-01-03-13-45-32.png" alt="crossmodel">
*Source: On Transferability of Prompt Tuning for Natural Language Processing*

Việc tái sử dụng trực tiếp Soft Prompt đã được đào tạo giữa các PLM khác nhau được coi là không khả thi do sự khác biệt về Embedding spaces[^fn1]. Vì vậy ta cần Projector để có thể chuyển đổi Soft Prompts giữa các mô hình.

Prompt Projector được đào tạo với các mục tiêu khác nhau và ta kiểm tra hiệu quả của chính bằng 2 cách:
- Min distance:
  - Prompt trained on source model: $P_s$
  - Prompt trained on target model: $P_t$
  - Minimize the distance between Projector($P_s$) and $P_t$
- Task tuning:
  - Directly tune the projected prompt Projector($P_s$)
  - Backpropagate the supervision signals to train the projector weights

<img src="https://i.ibb.co/ftFkzM2/Screenshot-from-2024-01-03-15-00-38.png" alt="ps">
*Source: On Transferability of Prompt Tuning for Natural Language Processing*

Quan sát việc chuyển giao prompt trong điều kiện zero-shot, ta nhận thấy phương pháp Task tuning có hiệu suất và tính tổng quát cao hơn so với Distance minimizing. Mặc dù mô hình khác nhau nhưng nếu có Prompt Projector ta vẫn có thể sử dụng lại Soft Prompt đã được học trước. Cross-Model Transfer dường như là khả thi.

### Transferability Indicator

Dựa trên kết quả tích cực trong Cross-Task và Cross-Model Transfer, ta sẽ tìm hiểu tại sao các Soft Prompts có thể chuyển giao giữa các task, model và điều gì quyết định khả năng chuyển giao giữa chúng. Ta có những cách sau đây:

- Embedding Similarity:
  - Đo độ tương đồng các câu prompt bằng các độ đo Euclidean hay Cosine.
  <center>
  <img src="https://i.ibb.co/NmS0hqT/kartinri-dlya-satimontazhnaya-oblast-15-2.jpg" alt="cluster">
  </center>
  - Ta có thể nhận xét rằng: các câu prompt thuộc cùng 1 loại tác vụ thì nằm gần nhau trong không gian embedding, hình thành nên các cluster. Như vậy, việc chuyển giao prompt giữa các tác vụ cùng loại là khả thi bởi vì bản chất các câu prompt này gần giống nhau. 
  
- Model Stimulation Similarity:
  - Đo độ tương đồng giữa các phản hồi của PLM bằng cách mô hình được kích hoạt (Overlapping rate of activated neurons - ON).
  - Mỗi câu prompt kích hoạt một vùng neuron khác nhau trong mạng neuron. Nếu 2 câu prompt cùng kích hoạt chung một vùng neuron thì 2 câu prompt có thể chuyển giao cho nhau.

## Conclusion
- Contribution: 
  - Prompt Transfer là một phương pháp đem lại hiệu suất tốt khi tận dụng việc học chuyển giao (Transfer learning) cho các Soft Prompt để lại kết quả tốt hơn trong quá trình Prompt Tuning, cải thiện được vấn đề thời gian khi so với Fine Tuning.
- Limitation: 
  - Prompt Projector hiện tại chưa được hoàn thiện và đang còn một số lỗi chưa khắc phục từ nhóm tác giả.

[Refactored source code](https://drive.google.com/drive/folders/1SA4fjbTZgULlaOdNWy8O4G9Rf9f9HkrQ?usp=drive_link)

*Cảm ơn bạn Minh-Dat Nguyen, Nhat-Truyen Nguyen đã cùng mình thực hiện phần trình bày này.*

[^fn1]: *Trong xử lý ngôn ngữ tự nhiên, Embedding spaces là biểu diễn của từ trong một không gian vector liên tục. Mỗi từ được ánh xạ thành một vector số thực, và các từ tương đồng được đặt gần nhau hơn trong không gian. Embedding spaces cho thấy mối quan hệ giữa các từ.*

