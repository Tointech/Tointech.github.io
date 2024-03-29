---
title: 'Fine Tuning and Challenges'
date: 2024-01-01 10:50:03 +0700
categories: [Research]
tags: [ai]     
---
## Fine Tuning
Fine tuning (tinh chỉnh) là 1 dạng của transfer learning. 

Transfer learning (học chuyển giao) là việc sử dụng những kiến thức đã học được từ vấn đề này và áp dụng chúng vào vấn đề  có liên quan khác. Một ví dụ đơn giản, thay vì phải tạo ra một mô hình hoàn toàn mới cho bài toán phân loại chó và mèo, ta có thể tận dụng một mô hình đã được huấn luyện trên tập dữ liệu ImageNet với hàng triệu hình ảnh. Việc tiếp tục huấn luyện mô hình này trên tập dữ liệu chó/mèo sẽ giúp quá trình diễn ra nhanh chóng và mang lại kết quả tốt hơn.

<img src="https://i.ibb.co/C70hCnC/1-CV81v-QUQTq-ko-ER9gvqjg.png" alt ="dogcat" width="500"/>
*Source: Ethan Yanjia Li*


Trong quá trình fine tuning, ta lấy một mô hình đã được huấn luyện trước đó, tận dụng một phần hoặc toàn bộ các lớp, và thêm/sửa/xóa một số lớp/nhánh để tạo ra một mô hình mới. Thông thường, các lớp ở phía trước của mô hình được freeze (đóng băng), nghĩa là trọng số của chúng sẽ không thay đổi trong quá trình huấn luyện. Lý do là các lớp này đã có khả năng trích xuất thông tin ở mức trừu tượng thấp, điều này đã được học từ quá trình huấn luyện trước đó. Đóng băng giúp tận dụng được khả năng trích xuất và giúp việc train diễn ra nhanh hơn, bởi vì lúc này model chỉ phải update weight ở các layer cao.

<img src="https://www.researchgate.net/publication/339658226/figure/fig2/AS:941670916952066@1601523250692/Two-types-of-fine-tuning-techniques-using-pretrained-model-trained-on-ImageNet-The-first.png" alt="finetune" width="500"/>
*Source: Multi-view Deep Representations with Cross-Dataset Transfer for Remote Sensing Image Retrieval and Classification*

Vì vậy có thể coi fine tuning là một bước tinh chỉnh tiêu chuẩn cho các mô hình.


## Challenges

Trong sự phát triển bùng nổ của các Pre-trained Language Model (PLM) hiện nay, các mô hình ngày càng gia tăng số lượng tham số từ hàng triệu đến hàng tỉ và hơn thế nữa.

> According to some sources, it is true that GPT-4 has 1.7 trillion parameters.

![PLMs](https://i.ibb.co/nPyN2d8/1-Ei-Qh-BVc-Ypp8sb-R0qn-L1rc-A.webp)
*Source: A Survey of LLMs*

Để fine tuning cho tất cả các tham số trên downstream task, đặc biệt là khi tập dữ liệu nhỏ, dễ dẫn đến việc overfitting. Vì lúc này mô hình chỉ đơn giản là ghi nhớ các dữ liệu huấn luyện thay vì học các patterns thật sự. Bên cạnh đó việc tinh chỉnh này còn đòi hỏi khối lượng và thời gian tính toán lớn.

Vì vậy, ta có những phương pháp Parameter-Efficient Fine Tuning (PEFT) được thiết kế để cố gắng làm cho quá trình tinh chỉnh tham số trở nên hiệu quả hơn, giảm thiểu lượng tham số cần được điều chỉnh. Ở PEFT sẽ tinh chỉnh một phần nhỏ các tham số cho downstream task trong khi đóng băng/cố định các tham số còn lại trong PLM. 

Ta có một số phương pháp được đề xuất trong PEFT:

<img src="https://i.ibb.co/ZMSd6xq/Screenshot-from-2024-01-01-23-21-35.png"  alt="peft" width="500"/>

Chi tiết về các phương pháp có ở trong PEFT mình sẽ trình bày ở những bài tiếp theo...
