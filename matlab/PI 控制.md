# PI 控制

比例积分算法在很多应用场景都用得到，这里讲解下使用MATLAB搭建一个PI控制器并生成代码。本示例来源于开源教程。


$$

$$

$$
\begin{align}
y(k) &= y_p(k) + y_i(k)\qquad 即 P + I 输出  \\
y_p(k) &= K_pe（k)\qquad 即e(k)指的是当前状态偏差 \\
y_i(k)&= y_i(k-1)+K_iT_se(k) 即y_i(k-1)就是上一次得值，T_s是什么呢？对是时间周期\\
PI控制器对我们来说需要标定就是K_p 和K_i根据这个公式我们使用MATLAB搭建模型
\end{align}
$$

