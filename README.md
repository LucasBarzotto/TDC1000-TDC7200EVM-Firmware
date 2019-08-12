# TDC1000-TDC7200EVM-Firmware
In this project I changed the firmware of the Texas Instruments board TDC1000-TDC7200EVM so it could work without the GUI and send the data through serial communication. The version of Code Composer Studio used was 6.1.0.

The main changes are:
 - The board now can work regardless the GUI i.e. it doesn't need USB communication.
 - It works with 5V supply (in the mini USB plug).
 - It sends its data through the GPIO3 pin from JP4 grouping (not populated) with Baud Rate 115200 bps.
 - For water flow measurement given in liters per second the output was modified to send the actual flow rate (for flow meter) in the following code (tdc7200_calculation.c):
 
          fluxoagua = (DELTATOF) *3551.662*K/1000000;
          sprintf((char *)outString, “Fluxo de água: %4.6f L/s”, fluxoagua); 
          
   Where K was found to be K = 0,7 through experiments. And the registers were modified to:
   
          TDC7200_reg_local_copy[10] = {0x82, 0x44, 0x00, 0x07, 0xFF, 0xFF, 0xFF, 0xFF, 0x00, 0x00 }; 
          TDC1000_reg_local_copy[10] = {0x45, 0x45, 0x12, 0x23, 0x1F, 0x80, 0x1E, 0x00, 0x33, 0x01 };
  
  - For water level measurement given in centimeters the code can be like:
  
          nivelagua = (start2stop[i])*640*100*1.15736/1000000000;
          sprintf((char *)outString, "Nível: %4.6f centímetros", nivelagua);
  
    And the registers could be:
   
          TDC7200_reg_local_copy[10] = {0x02, 0x40, 0x07, 0x07, 0xFF, 0xFF, 0xFF, 0xFF, 0x00, 0x00 }; 
          TDC1000_reg_local_copy[10] = {0x41, 0x41, 0x00, 0x04, 0x5F, 0x03, 0xFF, 0x00, 0x23, 0x00 };
 
   - These modifications were made aiming a prototype of an water flow meter that sends the data from GPIO3 to a ZigBEE module (Xbee3), so it could be transmited to a coordinator and also uploaded to the cloud using a gateway. Take a look in the picture.jpeg.
  - The original firmware can be found in the TDC1000_7200EVM_Firmware_Source-v2.01.zip.
  - The altered firmware can be found in the new_firmware_project.zip
  
 PS: don't forget to change the variable -printf_support from minimal to full on the CCS's project properties.
 

