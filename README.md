# Embedded-Coffee-Maker


Coffee Maker
Nicthe Nataly Jimenez
*Coffee Maker I/Os Overview
GPIO Inputs • On/Off (use Blue Button) • Strong Brew Button • Cup Size
Buttons (4)
*Coffee Maker GPIO Outputs
Power On/Off LED (Use LED2 on IoT Board) • Auto Off LED • Add Water
LED • Strong Brew LED • Brew Button LEDs (4 total)
*Coffee Maker Analog Input
Analog Inputs • Water Level (0-100%) (use A0 on Arduino) • Water
Temperature • Simulate using temp sensor on Discovery
*Coffee Maker Console Output 
• Use Console output similar to LCD on a
Coffee Maker • Display “Power On/Off” • Display Status Messages •
Example: Water Level • Example: Water Temperature • Example: Brewing
Started

Step 1 Step Startup STM32CubeIDE-> Step Access Board Selector
-> Step Enter “B-L475E-IOT01A” Board -> Step Select Board Photo -> Step
Select “Start Project” -> Step Select YES (initialize all peripherals with the
default mode) -> Step Setup STM32 project with project Name:
Embedded-C-Final-Assignment

Step 2 Default “Embedded-C-Final-Assignment” and default TrueStudio as
IDE

Step 3 Select Advanced Settings -> Step For Driver Selector, select HAL for
all that are available with HAL option


Step 4 Configuration GPIOs as Input Mode with Pull Up 
(connect a push-button switch to one of the GPIO input pins on the Arduino 
connector, then read the status of the on-off push button)
Buttons Pin out view
Buttons1 PD0
Buttons2 PD1
Buttons3 PD2
Buttons4 PD3

Step 5 Resulting Project -> Step Crete New BSP Folder under Drivers ->
Step Results (Notice new BSP Folder)->
Step Add BSP/B-L475E-IOT01 to include path: Properties, C/C++ Build,
Settings, Tool Settings, C Compiler, Directories, Include Path, Add ->
Step In main.c, add BSP related code header files

/* Private includes ----------------------------------------------------------*/

/* USER CODE BEGIN Includes */
#include "stm32l475e_iot01.h"//BSP_Tsensor needs it
#include "stm32l475e_iot01_tsensor.h"//BSP_Tsensor needs it
#include "stm32l475e_iot01_hsensor.h"//BSP_Tsensor needs it
#include "stdio.h"//Using printf()
/* USER CODE END Includes */

/*-----------------------------------------------------------------------------*/

Step 6  Initialize all configured peripherals BSP_TSENSOR_Init-BSP_HSENSOR_Init

/* Initialize all configured peripherals */

  BSP_TSENSOR_Init();
  BSP_HSENSOR_Init();

Step 7 Add code for HAL_UART_Transmit

/* USER CODE BEGIN 0 */

int __io_putchar(int ch){
	HAL_UART_Transmit(&huart1, (uint8_t*)&ch, 1, 1000);
	return 1;
}

/* USER CODE END 0 */

Step 8 Step 13. In main.c, add code to display the Coffee Maker

/***********************Code****************************/
 /* USER CODE BEGIN WHILE */

    int powerChanged = 0;

    int cupSize1ButtonChanged = 0;
    int cupSize2ButtonChanged = 0;
    int cupSize3ButtonChanged = 0;
    int cupSize4ButtonChanged = 0;

    int cupSizeNumber = 0;

    int powerPressed = 0;

    int cupSize1Pressed = 0;
    int cupSize2Pressed = 0;
    int cupSize3Pressed = 0;
    int cupSize4Pressed = 0;

 	 GPIO_PinState Cup_Size_Button_1 = 0;
 	 GPIO_PinState Cup_Size_Button_2 = 0;
 	 GPIO_PinState Cup_Size_Button_3 = 0;
 	 GPIO_PinState Cup_Size_Button_4 = 0;
    int Strong_Button = 1;

    /*-------------------------------TRANSMIT UART1 -----------------------------*/
      char *msg = "On:";
     char *msg4 = "Off:";
   	  int index = 0;
         int len = 5;
   	  int iterations = 10;

   			char * toArray(int number)
   			    {
   			        int n = log10(number) + 1;
   			        int i;
   			      char *numberArray = calloc(n, sizeof(char));
   			        for ( i = n-1; i >= 0; --i, number /= 10 )
   			        {
   			            numberArray[i] = (number % 10)+'0';
   			        }
   			        return numberArray;
   			    }

   			int numberLen(n) {
   				int count = 0;
   			    while (n != 0) {
   			        n /= 10;
   			        ++count;
   			    }
   			    return count;
   			}

     /* Infinite loop */

     /* USER CODE BEGIN WHILE */

     int uart = 1;

  /*-------------------------------TRANSMITE UART1----------------------------*/

     void printUart(char *msg) {
  	   for (int i = 0; i < strlen(msg); i++) {
  		   HAL_UART_Transmit(&huart1, &msg[i], 1, 0);
  		   HAL_Delay(10);
  	   }
  	   HAL_UART_Transmit(&huart1, "\n", 1, 0);
     }

  	void powerOff() {
  		powerChanged = 0;
  		cupSize1ButtonChanged = 0;
  		cupSize2ButtonChanged = 0;
  		cupSize3ButtonChanged = 0;
  		cupSize4ButtonChanged = 0;
  		printUart("Power OFF");
  		printUart("Bye");
  		HAL_GPIO_TogglePin(LED2_GPIO_Port, LED2_Pin);
  	}

  	void processCup1() {
  		 printUart("Cup Size Selected: 1");
  		 HAL_Delay(3000);
  		 printUart("Adding Water to Cup");
  		 HAL_GPIO_TogglePin(ARD_D8_GPIO_Port, ARD_D8_Pin);//Add_Water_LED_GPIO
  		 HAL_Delay(10000);
  		 if (BSP_HSENSOR_ReadHumidity() > 0) {
  			 printUart("Brewing Coffee");
  		 }
  		 HAL_Delay(8000);
  		 if (BSP_TSENSOR_ReadTemp() > 20) {
  			 printUart("Ready to drink, Enjoy!");
  		 }
  		HAL_Delay(1000);
  	}

  	void processCup2() {
  		 printUart("Cup Size Selected: 2");
  		 HAL_Delay(3000);
  		 printUart("Adding Water to Cup");
  		 HAL_GPIO_TogglePin(ARD_D8_GPIO_Port, ARD_D8_Pin);//Add_Water_LED_GPIO
  		 HAL_Delay(8000);
  		 if (BSP_HSENSOR_ReadHumidity() > 0) {
  			 printUart("Brewing Coffee");
  		 }
  		 HAL_Delay(6000);
  		 if (BSP_TSENSOR_ReadTemp() > 20) {
  			 printUart("Ready to drink, Enjoy!");
  		 }
  		HAL_Delay(1000);
  	}

  	void processCup3() {
  		 printUart("Cup Size Selected: 3");
  		 HAL_Delay(3000);
  		 /*if (strongChanged == 1) {
  			 printUart("Strong Coffee Selected");
  		 }*/
  		 printUart("Adding Water to Cup");
  		 HAL_GPIO_TogglePin(ARD_D8_GPIO_Port, ARD_D8_Pin);//Add_Water_LED_GPIO
  		 HAL_Delay(6000);
  		 if (BSP_HSENSOR_ReadHumidity() > 0) {
  			 printUart("Brewing Coffee");
  		 }
  		 HAL_Delay(4000);
  		 if (BSP_TSENSOR_ReadTemp() > 20) {
  			 printUart("Ready to drink, Enjoy!");
  		 }
  		HAL_Delay(1000);
  	}

  	void processCup4() {
  		 printUart("Cup Size Selected: 4");
  		 HAL_Delay(3000);
  		 printUart("Adding Water to Cup");
  		 HAL_GPIO_TogglePin(ARD_D8_GPIO_Port, ARD_D8_Pin);//Add_Water_LED_GPIO
  		 HAL_Delay(4000);
  		 if (BSP_HSENSOR_ReadHumidity() > 0) {
  			 printUart("Brewing Coffee");
  		 }
  		 HAL_Delay(2000);
  		 if (BSP_TSENSOR_ReadTemp() > 20) {
  			 printUart("Ready to drink, Enjoy!");
  		 }
  		HAL_Delay(1000);
  	}



    while (1)
      {

  	  uint32_t button = BSP_PB_GetState(BUTTON_USER);
  	  Cup_Size_Button_1 = HAL_GPIO_ReadPin(ARD_D0_GPIO_Port, ARD_D0_Pin);
  	  Cup_Size_Button_2 = HAL_GPIO_ReadPin(ARD_D1_GPIO_Port, ARD_D1_Pin);
  	  Cup_Size_Button_3 = HAL_GPIO_ReadPin(ARD_D2_GPIO_Port, ARD_D2_Pin);
  	  Cup_Size_Button_4 = HAL_GPIO_ReadPin(ARD_D3_GPIO_Port, ARD_D3_Pin);
  	  //GPIO_PinState Cup_Size_Button_5 = HAL_GPIO_ReadPin(ARD_D4_GPIO_Port, ARD_D4_Pin);

  	  //uint32_t Strong_Button = BSP_PB_GetState(Strong_Brew_Button);

  	  // --- Power Press --- //
  	  if (button == 1) {
  		 powerPressed = 0;
  	  }

  	  if (button == 0 && powerPressed == 0) {
  		  powerPressed = 1;
  		 if (powerChanged == 0) {
  			 powerChanged = 1;
  			 printUart("Power ON");
  			 HAL_GPIO_TogglePin(LED2_GPIO_Port, LED2_Pin);

  			 printUart("Select Cup Size: 1, 2, 3, 4");
  		 } else {
  			 powerOff();
  		 }
  	  }
  	  // --- Power Press --- //

  	  // --- Cup Size 1 Button --- //
  	  if (Cup_Size_Button_1 == GPIO_PIN_RESET) {
  		 cupSize1Pressed = 0;
  	  }

  	  if (Cup_Size_Button_1 == GPIO_PIN_SET && cupSize1Pressed == 0) {
  		  cupSize1Pressed = 1;
  		 if (cupSize1ButtonChanged == 0) {
  			 cupSize1ButtonChanged = 1;
  		 }
  	  }
  	  // --- Cup Size 1 Button --- //

  	  // --- Cup Size 2 Button --- //
  	  if (Cup_Size_Button_2 == GPIO_PIN_RESET) {
  		 cupSize2Pressed = 0;
  	  }

  	  if (Cup_Size_Button_2 == GPIO_PIN_SET && cupSize2Pressed == 0) {
  		  cupSize2Pressed = 1;
  		 if (cupSize2ButtonChanged == 0) {
  			 cupSize2ButtonChanged = 1;
  		 }
  	  }
  	  // --- Cup Size 2 Button --- //

  	  // --- Cup Size 3 Button --- //
  	  if (Cup_Size_Button_3 == GPIO_PIN_RESET) {
  		 cupSize3Pressed = 0;
  	  }

  	  if (Cup_Size_Button_3 == GPIO_PIN_SET && cupSize3Pressed == 0) {
  		  cupSize3Pressed = 1;
  		 if (cupSize3ButtonChanged == 0) {
  			 cupSize3ButtonChanged = 1;
  		 }
  	  }
  	  // --- Cup Size 3 Button --- //

  	  // --- Cup Size 4 Button --- //
  	  if (Cup_Size_Button_4 == GPIO_PIN_RESET) {
  		 cupSize4Pressed = 0;
  	  }

  	  if (Cup_Size_Button_4 == GPIO_PIN_SET && cupSize4Pressed == 0) {
  		  cupSize4Pressed = 1;
  		 if (cupSize4ButtonChanged == 0) {
  			 cupSize4ButtonChanged = 1;
  		 }
  	  }
  	  // --- Cup Size 4 Button --- //

  	  if (powerChanged == 1) {
  		  /*uint32_t Strong_Button = BSP_PB_GetState(Strong_Brew_Button);
  		  /*
  		 if(Strong_Button == 0)
  		 {
  		 		HAL_GPIO_TogglePin(Strong_Brew_LED_GPIO_Port, Strong_Brew_LED_Pin);//Strong_Brew_LED
  		 		HAL_Delay(1000);
  		 		BSP_LED_Off(Strong_Brew_LED_Pin);
  		 }
  		 else
  		 {
  		 */

  		  if(cupSize1ButtonChanged == 1)
  		  {
  			  HAL_GPIO_WritePin(ARD_D5_GPIO_Port, ARD_D5_Pin, GPIO_PIN_SET);
  			  HAL_GPIO_WritePin(ARD_D8_GPIO_Port, ARD_D8_Pin, GPIO_PIN_RESET);
  			  HAL_GPIO_WritePin(ARD_D9_GPIO_Port, ARD_D9_Pin, GPIO_PIN_RESET);
  			  HAL_GPIO_WritePin(ARD_D10_GPIO_Port, ARD_D10_Pin, GPIO_PIN_RESET);
  			  HAL_GPIO_WritePin(ARD_D14_GPIO_Port, ARD_D14_Pin, GPIO_PIN_RESET);

  			  cupSize2ButtonChanged == 0;
  			  cupSize3ButtonChanged == 0;
  			  cupSize4ButtonChanged == 0;

  			HAL_GPIO_TogglePin(ARD_A5_GPIO_Port, ARD_D5_Pin);//Led Auto_Off_LED_Pin

  		  	  processCup1();

  		  	powerOff();
  		  }
  		  else if(cupSize2ButtonChanged == 1)
  		  {
  			  HAL_GPIO_WritePin(ARD_D5_GPIO_Port, ARD_D5_Pin, GPIO_PIN_RESET);
  			  HAL_GPIO_WritePin(ARD_D8_GPIO_Port, ARD_D8_Pin, GPIO_PIN_SET);
  			  HAL_GPIO_WritePin(ARD_D9_GPIO_Port, ARD_D9_Pin, GPIO_PIN_RESET);
  			  HAL_GPIO_WritePin(ARD_D10_GPIO_Port, ARD_D10_Pin, GPIO_PIN_RESET);
  			  HAL_GPIO_WritePin(ARD_D14_GPIO_Port, ARD_D14_Pin, GPIO_PIN_RESET);

  			  cupSize1ButtonChanged == 0;
  			  cupSize3ButtonChanged == 0;
  			  cupSize4ButtonChanged == 0;

  			HAL_GPIO_TogglePin(ARD_A5_GPIO_Port, ARD_D5_Pin);//Led Auto_Off_LED_Pin

  			  processCup2();

  			powerOff();
  		  }
  		  else if(cupSize3ButtonChanged == 11)
  		  {
  			  HAL_GPIO_WritePin(ARD_D5_GPIO_Port, ARD_D5_Pin, GPIO_PIN_RESET);
  			  HAL_GPIO_WritePin(ARD_D8_GPIO_Port, ARD_D8_Pin, GPIO_PIN_RESET);
  			  HAL_GPIO_WritePin(ARD_D9_GPIO_Port, ARD_D9_Pin, GPIO_PIN_SET);
  			  HAL_GPIO_WritePin(ARD_D10_GPIO_Port, ARD_D10_Pin, GPIO_PIN_RESET);
  			  HAL_GPIO_WritePin(ARD_D14_GPIO_Port, ARD_D14_Pin, GPIO_PIN_RESET);

  			  cupSize2ButtonChanged == 0;
  			  cupSize1ButtonChanged == 0;
  			  cupSize4ButtonChanged == 0;

  			HAL_GPIO_TogglePin(ARD_A5_GPIO_Port, ARD_D5_Pin);//Led Auto_Off_LED_Pin

  			  processCup3();

  			powerOff();
  		  }
  		  else if(cupSize4ButtonChanged == 1)
  		  {
  			  HAL_GPIO_WritePin(ARD_D5_GPIO_Port, ARD_D5_Pin, GPIO_PIN_RESET);
  			  HAL_GPIO_WritePin(ARD_D8_GPIO_Port, ARD_D8_Pin, GPIO_PIN_RESET);
  			  HAL_GPIO_WritePin(ARD_D9_GPIO_Port, ARD_D9_Pin, GPIO_PIN_RESET);
  			  HAL_GPIO_WritePin(ARD_D10_GPIO_Port, ARD_D10_Pin, GPIO_PIN_SET);
  			  HAL_GPIO_WritePin(ARD_D14_GPIO_Port, ARD_D14_Pin, GPIO_PIN_RESET);

  			  cupSize2ButtonChanged == 0;
  			  cupSize3ButtonChanged == 0;
  			  cupSize1ButtonChanged == 0;

  			HAL_GPIO_TogglePin(ARD_A5_GPIO_Port, ARD_D5_Pin);//Led Auto_Off_LED_Pin

  			  processCup4();

  			powerOff();
  		  }

  	  }


      }
      /* USER CODE END 3 */
    }
 /**

/**
  * @brief System Clock Configuration
/***********************end_Code************************/

Step 9. Open Device Manager, plug in STM
board, Observe COM Port. Device Manager with COM3 to transmit with UART1

Step 10. Startup PuTTY on STM COM Port

Step 11. Build Project

Step 12. Hit Breakpoint, then click Resume

Step 13. Observe output on serial port





