# Select_PTAP_WaterFunds

Este Script permite seleccionar el tipo de planta de acuerdo con la calidad del agua estimada a partir de los modelos InVEST y de la normativa asociada a cada país. 

## Script

	# -*- coding: utf-8 -*-
	# Import Packages
	import os
	import pandas as pd

	def Select_PTAP(PathProject_PTAP):
	    # Leer Archivos de entrada
	    AWY = pd.read_csv( os.path.join(PathProject_PTAP,'INPUTS','1_WI_AWY.csv'))
	    N   = pd.read_csv( os.path.join(PathProject_PTAP,'INPUTS','3_WI_WN.csv'))
	    P   = pd.read_csv( os.path.join(PathProject_PTAP,'INPUTS','4_WI_WP.csv'))
	    S   = pd.read_csv( os.path.join(PathProject_PTAP,'INPUTS','2_WI_WSed.csv'))
	    NQ  = pd.read_csv( os.path.join(PathProject_PTAP,'INPUTS','7_NormQuality.csv'))
	    SelectQuality   = pd.read_csv( os.path.join(PathProject_PTAP,'INPUTS','6_Table_Select_Quality.csv'))
	    SelectPTAP      = pd.read_csv( os.path.join(PathProject_PTAP,'INPUTS','5_Table_Select_PTAP.csv'))

	    # Factor
	    # Kg/m3 -> mg/l
	    Factor_1 = 1000

	    # Integración de calidades
	    CN = N.sum(1)/AWY.sum(1)*Factor_1
	    CP = P.sum(1)/AWY.sum(1)*Factor_1
	    CS = S.sum(1)/AWY.sum(1)*Factor_1*1000

	    # Select PTAP - Water Quality - SST
	    if (CS[1] >= SelectPTAP['SST'][0]) and (CS[1] < SelectPTAP['SST'][1]):
	        Type_SST = 1
	    elif (CS[1] >= SelectPTAP['SST'][1]) and (CS[1] < SelectPTAP['SST'][2]):
	        Type_SST = 2
	    elif (CS[1] >= SelectPTAP['SST'][2]) and (CS[1] < SelectPTAP['SST'][3]):
	        Type_SST = 3
	    elif (CS[1] >= SelectPTAP['SST'][3]):
	        Type_SST = 4

	    # Select PTAP - Water Quality - N
	    if (CN[1] >= SelectPTAP['N'][0]) and (CN[1] < SelectPTAP['N'][1]):
	        Type_N = 1
	    elif (CN[1] >= SelectPTAP['N'][1]) and (CN[1] < SelectPTAP['N'][2]):
	        Type_N = 2
	    elif (CN[1] >= SelectPTAP['N'][2]) and (CN[1] < SelectPTAP['N'][3]):
	        Type_N = 3
	    elif (CN[1] >= SelectPTAP['N'][3]):
	        Type_N = 4

	    # Select PTAP - Water Quality - P
	    if (CP[1] >= SelectPTAP['P'][0]) and (CP[1] < SelectPTAP['P'][1]):
	        Type_P = 1
	    elif (CP[1] >= SelectPTAP['P'][1]) and (CP[1] < SelectPTAP['P'][2]):
	        Type_P = 2
	    elif (CP[1] >= SelectPTAP['P'][2]) and (CP[1] < SelectPTAP['P'][3]):
	        Type_P = 3
	    elif (CP[1] >= SelectPTAP['P'][3]):
	        Type_P = 4

	    # Water Quality
	    WQ = max([Type_SST, Type_N, Type_P])
	    # Normative Quality
	    NQ = NQ.values[0][0]

	    # Select PTAP - Water Quality + Normative
	    SelectQuality = SelectQuality.set_index('Quatily')
	    Tmp     = SelectQuality.values
	    FQ      = Tmp[WQ-1,NQ-1]
	    FQ_L    = ['','A','B','C','D','E','F','G']

	    Results = pd.DataFrame(data=[FQ_L[FQ]],columns=['Type'])
	    Results.to_csv( os.path.join(PathProject_PTAP,'OUTPUTS','1-Type_PTAP.csv'), index=False)



Este Script permite seleccionar el tipo de planta de acuerdo con la calidad del agua estimada a partir de los modelos InVEST y de la normativa asociada a cada país.

Como parámetros de entrada, el código requiere los siguientes archivos:
-	1_WI_AWY.csv
-	2_WI_WSed.csv
-	3_WI_WN.csv
-	4_WI_WP.csv
-	5_Table_Select_PTAP.csv
-	6_Table_SelectQuality.csv
-	7_NormQuality.csv
A continuación, se describen cada uno de los archivos

### 1_WI_AWYInputs.csv
Este archivo contiene las salidas de volumen de agua del algoritmo de balance, para el elemento CSinfra. La cantidad de columnas dependerá de la cantidad de captaciones que el usuario haya seleccionado para su caso de estudio. Mientras que la cantidad de filas corresponde al número de años. Para el caso particular de la WaterFund, este archivo solo contiene una fila. A continuación, se presenta su estructura para 3 entradas de CSinfra. 

|Time|Csinfra-1|Csinfra-2|Csinfra-3|
|--|--|--|--|
|1|100000|20000|50000|

Las unidades en las cuales deben ingresar los datos son en m^3

### 2_WI_WSedInputs.csv
Este archivo contiene las salidas de toneladas de sedimento del algoritmo de balance, para el elemento CSinfra. La cantidad de columnas dependerá de la cantidad de captaciones que el usuario haya seleccionado para su caso de estudio. Mientras que la cantidad de filas corresponde al número de años. Para el caso particular de la WaterFund, este archivo solo contiene una fila. A continuación, se presenta su estructura para 3 entradas de CSinfra. 

|Time|Csinfra-1|Csinfra-2|Csinfra-3|
|--|--|--|--|
|1|1000|200|50|

Las unidades en las cuales deben ingresar los datos son en Toneladas

### 3_WI_WNInputs.csv
Este archivo contiene las salidas de toneladas de nitrógeno del algoritmo de balance, para el elemento CSinfra. La cantidad de columnas dependerá de la cantidad de captaciones que el usuario haya seleccionado para su caso de estudio. Mientras que la cantidad de filas corresponde al número de años. Para el caso particular de la WaterFund, este archivo solo contiene una fila. A continuación, se presenta su estructura para 4 entradas de CSinfra. 

|Time|Csinfra-1|Csinfra-2|Csinfra-3|
|--|--|--|--|
|1|1000|200|50|

Las unidades en las cuales deben ingresar los datos son en Kilogramos

### 4_WI_WPInputs.csv
Este archivo contiene las salidas de toneladas de fosforo del algoritmo de balance, para el elemento CSinfra. La cantidad de columnas dependerá de la cantidad de captaciones que el usuario haya seleccionado para su caso de estudio. Mientras que la cantidad de filas corresponde al número de años. Para el caso particular de la WaterFund, este archivo solo contiene una fila. A continuación, se presenta su estructura para 4 entradas de CSinfra. 

|Time|Csinfra-1|Csinfra-2|Csinfra-3|
|--|--|--|--|
|1|1000|200|50|

Las unidades en las cuales deben ingresar los datos son en Kilogramos

### 5_Table_Select_PTAP.csv
Este archivo contiene los rangos de calidad del agua (concentraciones en mg/l) definidos por el especialista en plantas de tratamiento. 

<img src="https://github.com/The-Nature-Conservancy-NASCA/Select_PTAP_WaterFunds/blob/main/FIGURES/Fig_1.PNG" width="700" height="400" />

La estructura de este archivo se presenta a continuación:

|Quatily|SST|N|P|
|--|--|--|--|
|1|0|0|0
|2|4.4|10|0.17|
|3|56|20|0.24
|4|203|30|0.31|


### 6_Table_Select_Quality.csv
Este archivo contiene la tabla de clasificación de tipo de PTAP propuesta por el especialista en plantas de tratamiento.

<img src="https://github.com/The-Nature-Conservancy-NASCA/Select_PTAP_WaterFunds/blob/main/FIGURES/Fig_2.PNG" width="600" height="500" />

La numeración utilizada fue la siguiente:

|Tipo|Código|Tipo|Código|
|--|--|--|--|
|A|1|E|1|
|B|1|F|1|
|C|1|G|1|
|D|1|--|--|

Asi pues, la estructura de este archivo debe ser:

|Quatily|1|2|3|4|
|--|--|--|--|--|
|1|5|6|7|7|
|2|5|4|6|6|
|3|2|3|4|4|
|4|1|1|3|4|

### 7_NormQuality.csv
Este archivo contiene el tipo de plata (A, B, C, D, E, F, G) de acuerdo con la normativa de cada país. Los datos para alimentar este archivo fueron suministrados por el especialista en planta de tratamiento de agua potable. A continuación, e presenta la estructura de este archivo. 

|Quatily|
|--|
|1|

### Tester de la función

	# -----------------------------------------------------------------------------------
	# Tester
	# -----------------------------------------------------------------------------------
	PathProject_PTAP = r'C:\Users\jonathan.nogales\Music\Select_PTAP_WaterFunds\Project'
	Select_PTAP(PathProject_PTAP)
