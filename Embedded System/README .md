# stm32

## ADC (analog-to-digital conversion)類比數位訊號轉換、DAC

### Analog as Input

```.c
HAL_ADC_Start(ADC_HandleTypeDef* hadc) // 軟體開始ADC轉換

HAL_ADC_PollForConversion(ADC_HandleTypeDef* hadc, uint32_t Timeout) // 等待ADC轉換完成，timeout可自行設定(數字)

HAL_ADC_GetValue(ADC_HandleTypeDef* hadc) // 獲取取樣值
```

### Analog as Output

```.c
HAL_DAC_Start(DAC_HandleTypeDef* hdac,uint32_t Channel) // 軟體開始DAC轉換

HAL_DAC_Stop(DAC_HandleTypeDef* hdac,uint32_t Channel) // 停止DAC轉換

HAL_DAC_SetValue(DAC_HandleTypeDef* hdac,uint32_t Channel, uint32_t Alignment, uint32_t Data)
// 其中Alignment為選擇12bits向左或向右對齊ex:DAC_ALIGN_12B_R
```

## Digital Input/Output(GPIO)

```.c
HAL_GPIO_WritePin(GPIOx, GPIO_Pin, PinState) // 將指定腳位狀態設為high或low

HAL_GPIO_ReadPin(GPIOx, GPIO_Pin)
/*
讀取指定腳位狀態，值為high或low
@RETRUN_TYPE: GPIO_PinState
*/

HAL_GPIO_TogglePin(GPIOx, GPIO_Pin)
/*
改變指定腳位的狀態
@INFORMATION: 若要指定腳位為PB0，則GPIOx設為GPIOB，GPIO_Pin設為GPIO_PIN_0
*/

/*
@CONSTANT PinState
    GPIO_PIN_SET // high
    GPIO_PIN_RESET // low
*/
```
