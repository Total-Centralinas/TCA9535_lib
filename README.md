# TCA9535_lib
Biblioteca para placa de relés com TCA9535

Exemplo:

```C
void StartDefaultTask(void *argument)
{
  /* init code for USB_HOST */
  MX_USB_HOST_Init();
  /* USER CODE BEGIN StartDefaultTask */

  uint8_t value = 0;



  uint8_t config0 = 0x00; // 0 out - 1 IN
  uint8_t config1 = 0x00;

  uint8_t output_port0 = 0x00;
  uint8_t output_port1 = 0x00;  

  TCA9535_Init(&hi2c4, config0, config1, output_port0, output_port1);
  uint8_t outputs;


  TCA9535_WritePin(&hi2c4, TCA9535_PORT1, 6, 1);

  for(;;)
  {
	  HAL_I2C_Mem_Read(&hi2c4, I2C_ADDR, REG_OUTPUT_0, 1, &outputs, 1, HAL_MAX_DELAY);

	  // Set P0_1 HIGH (bit 1 = 1)
	  outputs = 1;
	  for(int i =0; i < 8; i++)
	  {

		 // HAL_I2C_Mem_Write(&hi2c4, I2C_ADDR, REG_OUTPUT_0, 1, &out, 1, HAL_MAX_DELAY);
		  TCA9535_WritePin(&hi2c4, TCA9535_PORT0, i, 1);
		  if(i == 0){
			  TCA9535_WritePin(&hi2c4, TCA9535_PORT0, 7, 0);
		  }else{
			  TCA9535_WritePin(&hi2c4, TCA9535_PORT0, i-1, 0);
		  }

		  if(TCA9535_ReadPin(&hi2c4, TCA9535_PORT0, 1) == 1){
			  TCA9535_WritePin(&hi2c4, TCA9535_PORT1, 1, 1);
		  }else{
			  TCA9535_WritePin(&hi2c4, TCA9535_PORT1, 1, 0);
		  }

		  vTaskDelay(pdMS_TO_TICKS(3000));
	  }


	  outputs = 0;
	  HAL_I2C_Mem_Write(&hi2c4, I2C_ADDR, REG_OUTPUT_0, 1, &outputs, 1, HAL_MAX_DELAY);

	  vTaskDelay(pdMS_TO_TICKS(1));

  }
  /* USER CODE END StartDefaultTask */
}
