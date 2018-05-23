# IRchatbot
实验环境：Python3 tensorflow 1.3
检索式聊天机器人：
输入：一段上下文和一个可能的候选回复
输出：这个候选回复的打分
使用Ubuntu数据集训练检索式聊天机器人
10000条训练数据，其中正例5000条（标签为1），负例5000条（负例中的回复为随机生成，标签为0），
每条样本由一段上下文（context）即query，和一个回复（utterance）组成即response，
如果这条样本的标签为1，说明这个回复是正确的回复，如果该样本标签为0，说明这个回复是错误的回复

模型的测评方式为recall@k，即经模型对候选回复（response）排序后，前k个回复中正例的占比（就是将测试集输入模型中，模型计算出每一个样本在所有回复上的打分，
选择打分排在前k个的回复作为输出，计算所有样本的k个候选回复中正确的占比，用回复对的样本数比总的测试样本数），显然取的k越大，这个指标（recall@k）越高，对模型
的要求越松。

训练和测试所使用的评测方式是不一样的，训练过程中我们对每个case可能作为正确回复的概率进行预测，
而测试过程中我们对每组数据（包含10个case，其中1个是正确的，另外9个是生成的负例/噪音数据）中的case进行逐条
概率预测，得到例如[0.34, 0.11, 0.22, 0.45, 0.01, 0.02, 0.03, 0.08, 0.33, 0.11]这样格式的输出，这些
输出值的和并不要求为1（因为是逐条预测的，有单独的预测概率值，在0到1之间）；而对于这组数据而言，因为数据index=0
对应的为正确答案，这里recall@1为0，因为0.34是其中第二大的值，所以recall@2是1（表示这组数据中预测概率值在前二
的中有一个是正确的）。
