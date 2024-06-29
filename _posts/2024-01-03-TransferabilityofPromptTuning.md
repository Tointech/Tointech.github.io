---
title: 'Transferability of Prompt Tuning'
date: 2024-01-03 12:52:03 +0700
categories: [NLP]
tags: [paper, finetuning]   
math: true  
---
Tóm tắt bài báo [On Transferability of Prompt Tuning for Natural Language Processing](https://aclanthology.org/2022.naacl-main.290.pdf),  NAACL 2022.

---

## Background
### Fine Tuning
Fine tuning (tinh chỉnh) là 1 dạng của transfer learning. 

Transfer learning (học chuyển giao) là việc sử dụng những kiến thức đã học được từ vấn đề này và áp dụng chúng vào vấn đề  có liên quan khác. Một ví dụ đơn giản, thay vì phải tạo ra một mô hình hoàn toàn mới cho bài toán phân loại chó và mèo, ta có thể tận dụng một mô hình đã được huấn luyện trên tập dữ liệu ImageNet với hàng triệu hình ảnh. Việc tiếp tục huấn luyện mô hình này trên tập dữ liệu chó/mèo sẽ giúp quá trình diễn ra nhanh chóng và mang lại kết quả tốt hơn.

<img src="https://i.ibb.co/C70hCnC/1-CV81v-QUQTq-ko-ER9gvqjg.png" alt ="dogcat" width="500"/>
*Source: Ethan Yanjia Li*


Trong quá trình fine tuning, ta lấy một mô hình đã được huấn luyện trước đó, tận dụng một phần hoặc toàn bộ các lớp, và thêm/sửa/xóa một số lớp/nhánh để tạo ra một mô hình mới. Thông thường, các lớp ở phía trước của mô hình được freeze (đóng băng), nghĩa là trọng số của chúng sẽ không thay đổi trong quá trình huấn luyện. Lý do là các lớp này đã có khả năng trích xuất thông tin ở mức trừu tượng thấp, điều này đã được học từ quá trình huấn luyện trước đó. Đóng băng giúp tận dụng được khả năng trích xuất và giúp việc train diễn ra nhanh hơn, bởi vì lúc này model chỉ phải update weight ở các layer cao.

<img src="https://www.researchgate.net/publication/339658226/figure/fig2/AS:941670916952066@1601523250692/Two-types-of-fine-tuning-techniques-using-pretrained-model-trained-on-ImageNet-The-first.png" alt="finetune" width="500"/>
*Source: Multi-view Deep Representations with Cross-Dataset Transfer for Remote Sensing Image Retrieval and Classification*

Vì vậy có thể coi fine tuning là một bước tinh chỉnh tiêu chuẩn cho các mô hình.


#### Challenges

Trong sự phát triển bùng nổ của các Pre-trained Language Model (PLM) hiện nay, các mô hình ngày càng gia tăng số lượng tham số từ hàng triệu đến hàng tỉ và hơn thế nữa.

> According to some sources, it is true that GPT-4 has 1.7 trillion parameters.

![PLMs](https://i.ibb.co/nPyN2d8/1-Ei-Qh-BVc-Ypp8sb-R0qn-L1rc-A.webp)
*Source: A Survey of LLMs*

Để fine tuning cho tất cả các tham số trên downstream task, đặc biệt là khi tập dữ liệu nhỏ, dễ dẫn đến việc overfitting. Vì lúc này mô hình chỉ đơn giản là ghi nhớ các dữ liệu huấn luyện thay vì học các patterns thật sự. Bên cạnh đó việc tinh chỉnh này còn đòi hỏi khối lượng và thời gian tính toán lớn.

Vì vậy, ta có những phương pháp Parameter-Efficient Fine Tuning (PEFT) được thiết kế để cố gắng làm cho quá trình tinh chỉnh tham số trở nên hiệu quả hơn, giảm thiểu lượng tham số cần được điều chỉnh. Ở PEFT sẽ tinh chỉnh một phần nhỏ các tham số cho downstream task trong khi đóng băng/cố định các tham số còn lại trong PLM. 

Ta có một số phương pháp được đề xuất trong PEFT:

<img src="https://i.ibb.co/ZMSd6xq/Screenshot-from-2024-01-01-23-21-35.png"  alt="peft" width="500"/>

### Prompt Tuning

Trong phần này sẽ giới thiệu định nghĩa về một phương pháp có ở trong PEFT là Prompt Tuning hay Soft Prompt Tuning.

#### Google Translate vs. GPT

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


#### Hard Prompt vs. Soft Prompt

<img src="https://www.researchgate.net/publication/366062946/figure/fig1/AS:11431281105340756@1670383256990/The-comparison-between-the-previous-T5-prompt-tuning-method-part-a-and-the-introduced.jpg" alt="hardprompt" width=500/>
*Source: Controlled Text Generation using T5 based Encoder-Decoder Soft Prompt Tuning and Analysis of the Utility of Generated Text in AI*

Ta thấy rằng hai thuật ngữ này khác nhau về cách ta tạo nên prompt:
- Khi tự tạo prompt bằng ngôn ngữ tự nhiên, ta nói về Hard Prompt.
- Khi prompt không phải do con người thiết kế mà được học với dữ liệu thì nó là Soft Prompt. Cụ thể, Soft Prompt bao gồm các tham số trong không gian vector và các tham số đó có thể được điều chỉnh trong quá trình đào tạo để thu được loss nhỏ hơn và câu trả lời cho ra sẽ tốt hơn.

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

Prompt Projector được đào tạo với các mục tiêu khác nhau và ta kiểm tra hiệu quả bằng 2 cách:
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

