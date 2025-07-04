Классификация $X \to \{0, 1, 2, ..., k\}$, где $k$ — количество классов  
Регрессия $X \to \mathbb{R}$

## Линейные функции

$y = w_0 + w_1x_1 + w_2x_2 + \dots + w_Dx_D$, где  
$y$ — таргет  
$x_1, x_2, \dots, x_D$ — вектор признаков, фичи  
$w_1, w_2, \dots, w_D, w_0$ — параметры модели или веса  
$w_0$ — сдвиг (bias)  
$y = \langle x, w \rangle + w_0$  
Цель — найти вектор $w$

**Плюсы:**
- Интерпретируемость ($w_i$ — чем больше, тем больше вклад в результат)

**Минусы:**
- Если сложная задача, то нужно придумывать дополнительные фичи, которые являются более сложными функциями от исходных (feature engineering)
- Если между признаками есть линейная зависимость, то коэффициенты могут потерять свой физический смысл (решается регуляризацией)
- Веса могут меняться в зависимости от обучающей выборки, хотя, чем больше данных, тем они сходятся к весам наилучшей линейной модели

## МНК (Метод наименьших квадратов)

МНК используется для решения задачи регрессии  
Зачастую свободный член $w_0$ опускают, добавляя единицу в вектор $x$  
$y = w_0 \cdot 1 + w_1x_1 + \dots + w_Dx_D \Rightarrow$  
$f_x(x_i) = \langle w, x_i \rangle$

Функция, которая позволяет узнать насколько часто модель ошибается, называется функцией потерь, функционалом качества или лоссом.


$$
L^p = \|y - Xw\|_p = \left( \sum_{i=1}^N (y_i - \langle w, x_i \rangle)^p \right)^{\frac{1}{p}}
$$

**$L^2$ — норма разницы (евклидово расстояние) между вектором таргетов и вектором ответов:**

$L(f, X, y) = \|y - f_w(x)\|_2^2 = \|y - Xw\|_2^2 = \sum_{i=1}^N (y_i - \langle w, x_i \rangle)^2$

### Mean Squared Error (MSE) или среднеквадратичная ошибка

$$\text{MSE}(f, X, y) = \frac{1}{N} \|y - Xw\|_2^2 = \frac{1}{N} \sum_{i=1}^N (y_i - \langle w, x_i \rangle)^2$$

Чтобы найти лучшие веса, нужно минимизировать $\|y - Xw\|_2^2$ по $w$

## МНК — точный аналитический метод

$$Xw = w_1x^{(1)} + \dots + w_Dx^{(D)}$$

$$
\begin{aligned}
L(f, X, y) &= (y - Xw)^T(y - Xw) \\
&= y^Ty - y^TXw - (Xw)^Ty + (Xw)^T(Xw) \\
&= y^Ty - 2X^Tw^Ty + w^T X^T X w \\
\end{aligned}
$$

(так как $y^TXw$ — число, то $y^TXw = (X^T w)^T y = w^T X^T y$)

Градиент:
$$\nabla_w L(f, X, y) = -2X^Ty + 2X^TXw = 0$$  
Решение:
$$w = (X^TX)^{-1}X^Ty$$

Сложность: $O(D^2N + D^3)$  
где $N$ — длина выборки, $D$ — число признаков

## МНК — приближённый численный метод

### Градиентный спуск

$$w_j \mapsto w_j - \alpha \frac{d}{dw_j} L(f_w, X, y)$$  
$\alpha$ — темп обучения

$$L(f_w, X, y) = \frac{1}{N} \|Xw - y\|^2$$

Градиент:
$$
\nabla_w L(f, X, y) = \frac{2}{N} X^T(Xw - y)
$$

- Функция выпуклая $\Rightarrow$ выбор начальной точки влияет на время сходимости, но можно начинать с нуля
- Число обусловленности матрицы $X$ существенно влияет на сходимость: чем более вытянуты эллипсоиды уровня функции потерь, тем хуже
- Темп обучения задаётся $\alpha$. При неправильном подборе алгоритм может не сходиться
- Другие гиперпараметры — количество итераций $S$ и/или порог $tolerance$

Сложность: $O(NDS)$  
Сложность по памяти: $O(ND)$

где $N$ — длина выборки, $D$ — число признаков, $S$ — число итераций

### Стохастический градиентный спуск

Так как для градиентного спуска требуется вычислять градиент по всей выборке ($O(ND)$), возникает идея оценивать градиент на подвыборке (batch или mini-batch)
