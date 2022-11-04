# 使用步骤

## 1.设置方程，需要将方程分为确定和随机两部分
    # 函数的定义需遵守t, y(可多维), ..., **kwargs 形式
    
    def deter_func(t, yn, alpha1, beta1, oumiga1, alpha2, beta2, oumiga2, a, b, **kwargs):
        Y = np.zeros(4) # 与方程维度一致
    
        Y[0] = yn[1]
        Y[1] = alpha1 * yn[1] - beta1 * yn[0] ** 2 * yn[1] - (oumiga1 ** 2) * yn[0] - a * yn[2] - b * ((yn[0] - yn[2]) ** 3)
        Y[2] = yn[3]
        Y[3] = (alpha1 - alpha2) * yn[3] - beta2 * (yn[2] ** 2) * yn[3] - (oumiga2 ** 2) * yn[2] - a * yn[0] - b * ((yn[2] - yn[0]) ** 3)
        return Y
    
    def rand_func(t, yn, c1, c2, h, D1, D2, **kwargs):
        # 因为每次去算k的时候噪声都会重新生成，所以切忌把这个函数并入确定性方程中
        Y = np.zeros(4)  # 与方程维度一致
    
        bro = [np.random.standard_normal() * np.sqrt(2 * h * D1), np.random.standard_normal() * np.sqrt(2 * h * D2)] # 生成高斯白噪声
    
        Y[0] = 0
        Y[1] = c1*yn[0]*bro[0]
        Y[2] = 0
        Y[3] = c2*yn[2]*bro[1]
        return Y

## 2.设置求解超参数和方程里的参数

    para_dict = {
        # 求解设置
        'tspan': 10000,
        'h': 0.01,
        'rand_open': True,
        'slvtype': 'RK4',
    
    
        # 参数设置
        'alpha1': 0.01,
        'alpha2': 0.01,
        'beta1': 0.01,
        'beta2': 0.02,
        'oumiga1': 1,
        'oumiga2': 2,
        'a': 0.01,
        'b': 1,
        'c1': 1,
        'c2': 1,
        'D1': 0.3, # 噪声强度
        'D2': 0.3,
    }


## 3.调用.solver()方法求解

    desolver = Desolver(deter_func, rand_func, y0, **para_dict)
    data_solve = desolver.solver() 
    # 返回与方程同维度时间序列数据