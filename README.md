Hi there 👋


Rosa Elena Peña (21-0415) 
Francisco Adolfo (20-0866)




# Importar modulos

import mysql.connector
import numpy as np
import pandas as pd
import matplotlib.pyplot as plt
plt.style.use('fivethirtyeight')



conexion = mysql.connector.connect(user='root', password='root',
                                    host='localhost', 
                                    database='bd_bolsadevalores',
                                    port='3306')

                                
print(conexion)



datos = pd.read_csv('GOOGL.csv')
datos.head()



# Graficar los datos
plt.figure(figsize = (10, 5))
plt.plot(datos['Close'], label = 'Google Stock')
plt.title('Acciones de Yahoo Finance - Precio de las Acciones desde el 2016 al 2022')
plt.xlabel('14 de Enero 2016 al 14 de Agosto 2022')
plt.ylabel('Precio de cierre ($)')
plt.legend('Google')
plt.show()



MVS30 = pd.DataFrame()
MVS30['Close'] = datos['Close'].rolling(window = 30).mean()
MVS30



MVS30[MVS30.index == 29]



MVS100 = pd.DataFrame()
MVS100['Close'] = datos['Close'].rolling(window = 100).mean()
MVS100



MVS100[MVS100.index == 99]



# Graficar los datos

plt.figure(figsize = (10, 5))
plt.plot(datos['Close'], label = 'Google Stock')
plt.plot(MVS30['Close'], label = 'Media Movil 30')
plt.plot(MVS100['Close'], label = 'Media Movil 100')
plt.title('Acciones de Yahoo Finance - Precio de las Acciones desde el 2016 al 2022')
plt.xlabel('14 de Enero 2016 al 14 de Agosto 2022')
plt.ylabel('Precio de cierre ($)')
plt.legend(loc = 'upper left')
plt.show()



data = pd.DataFrame()
data['Google'] = datos['Close']
data['MVS30'] = MVS30['Close']
data['MVS100'] = MVS100['Close']
data



def senal(data):
    compra = []
    venta = []
    condicion = 0
    
    for dia in range(len(data)):
        
        if data['MVS30'][dia] > data['MVS100'][dia]:
            if condicion != 1:
                compra.append(data['Google'][dia])
                venta.append(np.nan)
                condicion = 1
            else:
                compra.append(np.nan)
                venta.append(np.nan)
            
        elif data['MVS30'][dia] < data['MVS100'][dia]:
            if condicion != -1:
                venta.append(data['Google'][dia])
                compra.append(np.nan)
                condicion = -1
            else:
                compra.append(np.nan)
                venta.append(np.nan)
        else:
            compra.append(np.nan)
            venta.append(np.nan)
            
    return (compra, venta) 
    
    
    
    senales = senal(data)
data['Compra'] = senales[0]
data['Venta'] = senales[1]
data




#Grafica Yahoo Finance 

plt.figure(figsize = (10, 5))
plt.plot(data['Google'], label = 'Google', alpha = 0.3)
plt.plot(data['MVS30'], label = 'MVS30', alpha = 0.3)
plt.plot(data['MVS100'], label = 'MVS100', alpha = 0.3)
plt.scatter(data.index, data['Compra'], label = 'Precio de Compra', marker = '^', color = 'green')
plt.scatter(data.index, data['Venta'], label = 'Precio de Venta', marker = 'v', color = 'red')
plt.title('Acciones de Yahoo Finance - Precio de sus acciones desde el 2016 - 2022')
plt.xlabel('14 Enero 2016 - 14 Agosto. 2022')
plt.ylabel('Precio de cierre ($)')
plt.legend(loc = 'upper left')
plt.show()

