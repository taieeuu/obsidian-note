## Confusion Matrix
先用二元分類來說明混淆矩陣，混淆矩陣就是由實際是正樣本(Positive)和負樣本(Negative)，與預測正確(True)和錯誤(False)四種情況所形成的矩陣，四種情況分別為：

1. True Positive(TP)：實際是正樣本且預測為正樣本  
2. False Positive(FP)：實際是負樣本但誤判為正樣本  
3. False Negative(FN)：實際是正樣本但誤判為負樣本  
4. True Negative(TN)：實際是負樣本且預測為負樣本

	 ![[Confusion Matrix (1).png|450]]
	 
其中TP和TN代表預測正確，所以前面提到的準確率(Accuracy)又可表示為

> Accuracy = (TP+TN) / (TP+FP+FN+TN)

若是用在多元分類模型，Confusion Matrix的橫軸一樣可以是實際值，縱軸是預測值，可以瞭解每個類別的預測準確率，下圖為多元分類的混淆矩陣。

![[Confusion Matrix (2).png|450]]