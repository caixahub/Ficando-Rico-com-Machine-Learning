
```python
        Usando as dicas de carteira de investimentos do Barsi propusemos uma questão é possivel ficar
        rico usando um modelo preditor e se é qual o melhor para o projeto ?
        
        O LSTM é indicado por muitos o melhor modelo na atualidade para tratar series temporais mas em
        comparação com o Keras foi nescessário mais rodadas e mais tempo com o codigo para chegar ao 
        mesmo resultado comparando com o Keras, mas no final os dois se mostram muito bons para a finalidade.
        
        O projeto tem como base o exercicio do professor  Rdrigo Correia entre outros  
        https://www.linkedin.com/pulse/prevendo-pre%C3%A7o-de-a%C3%A7%C3%B5es-com-deep-learning-lstm-rodrigo-correa/
        foram feitas modificações para o uso do codigo, e exite uma pequena falha no codigo original
```


```python
import yfinance as yf
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
```


```python
Stock = pd.DataFrame(yf.Ticker('UNIP6.SA').history(period = '5y'))
```


```python
df = Stock.reset_index()
```


```python
df
```





  <div id="df-c2a375d1-8800-4ff5-a6fa-df49c9c2b61e">
    <div class="colab-df-container">
      <div>

<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Date</th>
      <th>Open</th>
      <th>High</th>
      <th>Low</th>
      <th>Close</th>
      <th>Volume</th>
      <th>Dividends</th>
      <th>Stock Splits</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>2018-04-06 00:00:00-03:00</td>
      <td>13.812444</td>
      <td>14.145848</td>
      <td>13.579062</td>
      <td>13.579062</td>
      <td>90580</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2018-04-09 00:00:00-03:00</td>
      <td>13.645739</td>
      <td>13.817204</td>
      <td>13.217077</td>
      <td>13.426645</td>
      <td>93660</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2018-04-10 00:00:00-03:00</td>
      <td>13.574295</td>
      <td>13.812441</td>
      <td>12.616951</td>
      <td>13.436172</td>
      <td>171220</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>3</th>
      <td>2018-04-11 00:00:00-03:00</td>
      <td>13.331390</td>
      <td>13.507617</td>
      <td>12.645530</td>
      <td>13.183739</td>
      <td>115640</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>4</th>
      <td>2018-04-12 00:00:00-03:00</td>
      <td>13.217077</td>
      <td>13.802915</td>
      <td>13.217077</td>
      <td>13.417119</td>
      <td>87780</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>1236</th>
      <td>2023-03-31 00:00:00-03:00</td>
      <td>71.559998</td>
      <td>72.389999</td>
      <td>69.599998</td>
      <td>70.050003</td>
      <td>406100</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>1237</th>
      <td>2023-04-03 00:00:00-03:00</td>
      <td>70.730003</td>
      <td>70.730003</td>
      <td>69.169998</td>
      <td>69.250000</td>
      <td>221700</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>1238</th>
      <td>2023-04-04 00:00:00-03:00</td>
      <td>69.650002</td>
      <td>71.000000</td>
      <td>69.650002</td>
      <td>69.970001</td>
      <td>200900</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>1239</th>
      <td>2023-04-05 00:00:00-03:00</td>
      <td>70.199997</td>
      <td>70.879997</td>
      <td>69.320000</td>
      <td>69.930000</td>
      <td>157500</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>1240</th>
      <td>2023-04-06 00:00:00-03:00</td>
      <td>70.029999</td>
      <td>70.870003</td>
      <td>69.320000</td>
      <td>69.449997</td>
      <td>244000</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
  </tbody>
</table>
<p>1241 rows × 8 columns</p>
</div>
     




```python
### Acertando o DataFrame que contem dados perdidos.
      
# some missing values, and we will use the fillna method to resolve the missing values.

# handing missing values
df.fillna(method='ffill', inplace = True) # use front fill method
df.fillna(method='bfill', inplace = True) # use back fill method
#check to see if there are still any missing values
df.isnull().sum()
```




    Date            0
    Open            0
    High            0
    Low             0
    Close           0
    Volume          0
    Dividends       0
    Stock Splits    0
    dtype: int64




```python
#Importando as libraries
from sklearn.preprocessing import MinMaxScaler
from keras.models import Sequential
from keras.layers import Dense, Dropout, LSTM
```


```python
#Criando o Dataframe
data = df.sort_index(ascending=True, axis=0)
new_data = pd.DataFrame(index=range(0,len(df)),columns=['Date', 'Close'])
for i in range(0,len(data)):
    new_data['Date'][i] = data['Date'][i]
    
    new_data['Close'][i] = data['Close'][i]
```


```python
#Colocando data como índice
new_data.index = new_data.Date
new_data.drop('Date', axis=1, inplace=True)
```


```python
#Criando o train e o test set
dataset = new_data.values


train = dataset[0:1100,:]
valid = dataset[1100:,:]
```


```python
### Realizando uma transformação escalar para que se possa trabalharcom o Numpy
      
scaler = MinMaxScaler(feature_range=(0, 1))
scaled_data = scaler.fit_transform(dataset)
```


```python
x_train, y_train = [], []
for i in range(90,len(train)):
    x_train.append(scaled_data[i-90:i,0])
    y_train.append(scaled_data[i,0])
x_train, y_train = np.array(x_train), np.array(y_train)


x_train = np.reshape(x_train, (x_train.shape[0],x_train.shape[1],1))

```


```python
# Criando o modelo LSTM
model = Sequential()
model.add(LSTM(units=50, return_sequences=True, input_shape=(x_train.shape[1],1)))

model.add(LSTM(units=50))
```


```python
model.compile(loss='mean_squared_error', optimizer='adam')
model.fit(x_train, y_train, epochs=32, batch_size=1, verbose=2)
```

    Epoch 1/32
    1010/1010 - 16s - loss: 0.0060 - 16s/epoch - 16ms/step
    Epoch 2/32
    1010/1010 - 7s - loss: 0.0018 - 7s/epoch - 6ms/step
    Epoch 3/32
    1010/1010 - 7s - loss: 0.0015 - 7s/epoch - 7ms/step
    Epoch 4/32
    1010/1010 - 7s - loss: 0.0010 - 7s/epoch - 6ms/step
    Epoch 5/32
    1010/1010 - 7s - loss: 9.5159e-04 - 7s/epoch - 7ms/step
    Epoch 6/32
    1010/1010 - 7s - loss: 6.7396e-04 - 7s/epoch - 7ms/step
    Epoch 7/32
    1010/1010 - 7s - loss: 7.8889e-04 - 7s/epoch - 7ms/step
    Epoch 8/32
    1010/1010 - 7s - loss: 6.0051e-04 - 7s/epoch - 7ms/step
    Epoch 9/32
    1010/1010 - 7s - loss: 5.6313e-04 - 7s/epoch - 6ms/step
    Epoch 10/32
    1010/1010 - 7s - loss: 4.7352e-04 - 7s/epoch - 7ms/step
    Epoch 11/32
    1010/1010 - 6s - loss: 4.6112e-04 - 6s/epoch - 6ms/step
    Epoch 12/32
    1010/1010 - 7s - loss: 4.0392e-04 - 7s/epoch - 7ms/step
    Epoch 13/32
    1010/1010 - 6s - loss: 3.8633e-04 - 6s/epoch - 6ms/step
    Epoch 14/32
    1010/1010 - 7s - loss: 2.8266e-04 - 7s/epoch - 7ms/step
    Epoch 15/32
    1010/1010 - 7s - loss: 3.8244e-04 - 7s/epoch - 7ms/step
    Epoch 16/32
    1010/1010 - 7s - loss: 3.3556e-04 - 7s/epoch - 7ms/step
    Epoch 17/32
    1010/1010 - 7s - loss: 2.9145e-04 - 7s/epoch - 7ms/step
    Epoch 18/32
    1010/1010 - 6s - loss: 3.2417e-04 - 6s/epoch - 6ms/step
    Epoch 19/32
    1010/1010 - 7s - loss: 3.2044e-04 - 7s/epoch - 7ms/step
    Epoch 20/32
    1010/1010 - 6s - loss: 3.4598e-04 - 6s/epoch - 6ms/step
    Epoch 21/32
    1010/1010 - 7s - loss: 2.8698e-04 - 7s/epoch - 7ms/step
    Epoch 22/32
    1010/1010 - 7s - loss: 2.6362e-04 - 7s/epoch - 7ms/step
    Epoch 23/32
    1010/1010 - 7s - loss: 3.0911e-04 - 7s/epoch - 7ms/step
    Epoch 24/32
    1010/1010 - 7s - loss: 2.4153e-04 - 7s/epoch - 7ms/step
    Epoch 25/32
    1010/1010 - 7s - loss: 2.6352e-04 - 7s/epoch - 6ms/step
    Epoch 26/32
    1010/1010 - 7s - loss: 2.3754e-04 - 7s/epoch - 7ms/step
    Epoch 27/32
    1010/1010 - 6s - loss: 2.7549e-04 - 6s/epoch - 6ms/step
    Epoch 28/32
    1010/1010 - 7s - loss: 2.4489e-04 - 7s/epoch - 7ms/step
    Epoch 29/32
    1010/1010 - 6s - loss: 2.8938e-04 - 6s/epoch - 6ms/step
    Epoch 30/32
    1010/1010 - 7s - loss: 2.2371e-04 - 7s/epoch - 7ms/step
    Epoch 31/32
    1010/1010 - 7s - loss: 2.6099e-04 - 7s/epoch - 7ms/step
    Epoch 32/32
    1010/1010 - 7s - loss: 2.4920e-04 - 7s/epoch - 7ms/step





    <keras.callbacks.History at 0x7f2e50266130>




```python
#Prevendo os 143 últimos preços de ação, baseado nos 90 últimos.

inputs = new_data[len(new_data) - len(valid) - 90:].values
inputs = inputs.reshape(-1,1)
inputs  = scaler.transform(inputs)

X_test = []
for i in range(90,inputs.shape[0]):
    X_test.append(inputs[i-90:i,0])
X_test = np.array(X_test)

X_test = np.reshape(X_test, (X_test.shape[0],X_test.shape[1],1))
closing_price = model.predict(X_test)
```

    5/5 [==============================] - 1s 7ms/step



```python
closing_price 
```




    array([[0.7888307 , 0.7847918 , 0.7820348 , ..., 0.7868334 , 0.7857554 ,
            0.78779083],
           [0.7971418 , 0.7926901 , 0.7910548 , ..., 0.7960812 , 0.7943751 ,
            0.7964841 ],
           [0.8001473 , 0.79610574, 0.79397774, ..., 0.7991686 , 0.7973041 ,
            0.7992701 ],
           ...,
           [0.58017844, 0.58013624, 0.5777181 , ..., 0.5817317 , 0.576314  ,
            0.58033735],
           [0.5901168 , 0.5900103 , 0.5874816 , ..., 0.59167254, 0.5865874 ,
            0.5903131 ],
           [0.5867607 , 0.5871907 , 0.58391553, ..., 0.58804834, 0.582906  ,
            0.5867018 ]], dtype=float32)




```python
      
### Desvazendo a transformação escalar feita no Dataframe  para trabalhar como o Numpy.      
closing_price = scaler.inverse_transform(closing_price)[:, [0]]
```


```python
#Visualizando a Previsão
plt.rcParams.update({'font.size': 22})


plt.figure(figsize=(20,10))
train = new_data[:1100]
t_2020 = train['2020']
valid = new_data[1100:]
valid['Predictions'] = closing_price
plt.ylabel('Preço da Ação')
plt.xlabel('Data')
plt.plot(train['Close'], label = "Treino")
plt.plot(valid['Close'], label = 'Observado')
plt.plot(valid['Predictions'], label = 'Previsão')
plt.legend(bbox_to_anchor=(0., 1.02, 1., .102), loc='lower left',
           ncol=2, mode="expand", borderaxespad=0.)
```

  




![output_16_2](https://user-images.githubusercontent.com/118861107/230736395-1af8dac6-7eb4-4af7-96a6-2f51f994a365.png)


    

    



```python
plt.rcParams.update({'font.size': 22})


plt.figure(figsize=(20,10))
train = new_data[:1100]
valid = new_data[1100:]
valid['Predictions'] = closing_price
plt.ylabel('Preço da Ação')
plt.xlabel('Data')
plt.plot(t_2020['Close'], label = "Treino")
plt.plot(valid['Close'], label = 'Observado')
plt.plot(valid['Predictions'], label = 'Previsão')
plt.legend(bbox_to_anchor=(0., 1.02, 1., .102), loc='lower left',
           ncol=2, mode="expand", borderaxespad=0.)
```





    
![output_17_2](https://user-images.githubusercontent.com/118861107/230736405-113f7e4c-a23c-45c9-b5ca-49cb8e1c245a.png)


    

    



```python
valid[['Close', 'Predictions']].tail(20)
```





  <div id="df-8bc2b1b9-a437-4bab-b166-355a413c5b8e">
    <div class="colab-df-container">
      <div>



<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Close</th>
      <th>Predictions</th>
    </tr>
    <tr>
      <th>Date</th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>2023-03-10 00:00:00-03:00</th>
      <td>77.709999</td>
      <td>77.067291</td>
    </tr>
    <tr>
      <th>2023-03-13 00:00:00-03:00</th>
      <td>75.68</td>
      <td>75.866264</td>
    </tr>
    <tr>
      <th>2023-03-14 00:00:00-03:00</th>
      <td>76.410004</td>
      <td>73.873619</td>
    </tr>
    <tr>
      <th>2023-03-15 00:00:00-03:00</th>
      <td>75.110001</td>
      <td>75.117294</td>
    </tr>
    <tr>
      <th>2023-03-16 00:00:00-03:00</th>
      <td>74.629997</td>
      <td>73.191513</td>
    </tr>
    <tr>
      <th>2023-03-17 00:00:00-03:00</th>
      <td>76.769997</td>
      <td>73.129395</td>
    </tr>
    <tr>
      <th>2023-03-20 00:00:00-03:00</th>
      <td>81.0</td>
      <td>75.474205</td>
    </tr>
    <tr>
      <th>2023-03-21 00:00:00-03:00</th>
      <td>75.0</td>
      <td>79.355827</td>
    </tr>
    <tr>
      <th>2023-03-22 00:00:00-03:00</th>
      <td>72.730003</td>
      <td>71.716965</td>
    </tr>
    <tr>
      <th>2023-03-23 00:00:00-03:00</th>
      <td>69.699997</td>
      <td>71.511322</td>
    </tr>
    <tr>
      <th>2023-03-24 00:00:00-03:00</th>
      <td>71.5</td>
      <td>67.774902</td>
    </tr>
    <tr>
      <th>2023-03-27 00:00:00-03:00</th>
      <td>71.639999</td>
      <td>70.752708</td>
    </tr>
    <tr>
      <th>2023-03-28 00:00:00-03:00</th>
      <td>73.650002</td>
      <td>70.000381</td>
    </tr>
    <tr>
      <th>2023-03-29 00:00:00-03:00</th>
      <td>71.639999</td>
      <td>72.533592</td>
    </tr>
    <tr>
      <th>2023-03-30 00:00:00-03:00</th>
      <td>71.089996</td>
      <td>69.503197</td>
    </tr>
    <tr>
      <th>2023-03-31 00:00:00-03:00</th>
      <td>70.050003</td>
      <td>69.751831</td>
    </tr>
    <tr>
      <th>2023-04-03 00:00:00-03:00</th>
      <td>69.25</td>
      <td>68.398117</td>
    </tr>
    <tr>
      <th>2023-04-04 00:00:00-03:00</th>
      <td>69.970001</td>
      <td>67.780388</td>
    </tr>
    <tr>
      <th>2023-04-05 00:00:00-03:00</th>
      <td>69.93</td>
      <td>68.771156</td>
    </tr>
    <tr>
      <th>2023-04-06 00:00:00-03:00</th>
      <td>69.449997</td>
      <td>68.436584</td>
    </tr>
  </tbody>
</table>
</div>
     
