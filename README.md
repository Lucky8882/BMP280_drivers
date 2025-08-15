# BMP280 Driver for STM32F411CEU6 (Black Pill)

This project provides a complete driver for the BMP280 temperature and pressure sensor, designed specifically for the STM32F411CEU6 microcontroller (Black Pill board). The driver uses I2C communication to interface with the sensor and offers easy-to-use APIs for initialization, reading sensor data, and configuration.

---

## Device Overview

**BMP280** is a digital barometric pressure sensor with an integrated temperature sensor. It provides accurate measurements of atmospheric pressure and temperature, commonly used in weather stations, altitude sensing, and environmental monitoring.

**Microcontroller:** STM32F411CEU6 (Black Pill)
**Communication Interface:** I2C

---

## Project Files

- `bmp280.c` - Main driver implementation including sensor initialization, calibration reading, and data compensation algorithms.
- `bmp280_ll.c` - Low-level I2C communication functions to read/write BMP280 registers.
- `main.c` - Example application demonstrating how to initialize the sensor and read temperature and pressure data.

---

## API Details

### `HAL_StatusTypeDef bmp280_init(I2C_HandleTypeDef *hi2c, uint8_t addr, bmp280_calib_t *calib)`

Initializes the BMP280 sensor. This function performs the following steps:
- Reads the sensor ID and verifies it matches BMP280 (0x58).
- Performs a soft reset of the sensor.
- Reads calibration data from the sensor's non-volatile memory into the provided `bmp280_calib_t` struct.
- Configures the sensor to normal mode with temperature and pressure oversampling set to x1, standby time 0.5ms, and filter off.

Returns `HAL_OK` on success or `HAL_ERROR` on failure.

---

### `HAL_StatusTypeDef bmp280_read_id(I2C_HandleTypeDef *hi2c, uint8_t addr, uint8_t *id)`

Reads the BMP280 sensor ID register. The expected ID value is 0x58.

Parameters:
- `hi2c`: Pointer to the I2C handle.
- `addr`: I2C address of the BMP280 sensor.
- `id`: Pointer to a variable to store the read ID.

Returns `HAL_OK` on success.

---

### `HAL_StatusTypeDef bmp280_read_data(I2C_HandleTypeDef *hi2c, uint8_t addr, bmp280_calib_t *calib, bmp280_data_t *data)`

Reads raw temperature and pressure data from the sensor, applies compensation algorithms using calibration data, and fills the `bmp280_data_t` struct with compensated values.

- Temperature is returned in hundredths of degrees Celsius (e.g., 2534 means 25.34°C).
- Pressure is returned in Pascals.

Returns `HAL_OK` on success.

---

### Low-Level I2C Communication APIs (in `bmp280_ll.c`)

These functions handle direct register read/write operations over I2C:

- `HAL_StatusTypeDef bmp280_write_reg(I2C_HandleTypeDef *hi2c, uint8_t addr, uint8_t reg, uint8_t data)`
  Writes a single byte to a BMP280 register.

- `HAL_StatusTypeDef bmp280_read_reg(I2C_HandleTypeDef *hi2c, uint8_t addr, uint8_t reg, uint8_t *data)`
  Reads a single byte from a BMP280 register.

- `HAL_StatusTypeDef bmp280_read_regs(I2C_HandleTypeDef *hi2c, uint8_t addr, uint8_t reg, uint8_t *data, uint16_t len)`
  Reads multiple bytes from consecutive BMP280 registers.

These are used internally by the main driver and typically not called directly by user code.

---

## Example Usage (from `main.c`)

1. Initialize the BMP280 sensor by calling `bmp280_init()`.
2. Verify the sensor ID with `bmp280_read_id()`.
3. Set the sensor to normal mode with oversampling.
4. Periodically read temperature and pressure data using `bmp280_read_data()`.
5. Output the readings via UART for monitoring.

---

## Hardware Connections

- Connect BMP280 SDA to STM32 I2C SDA pin.
- Connect BMP280 SCL to STM32 I2C SCL pin.
- Provide 3.3V power and ground.
- Use pull-up resistors on SDA and SCL lines if not present on the sensor module.

---

## Notes

- The driver uses floating-point compensation calculations internally but returns integer values scaled (temperature in 0.01°C, pressure in Pa).
- The sensor is configured for normal mode with minimal oversampling for low power consumption.
- Modify `bmp280_init()` if you want to change sensor settings like oversampling or filter.

---

## Contact

For questions or contributions, please open an issue or submit a pull request.

---

Thank you for using this BMP280 driver on STM32F411CEU6!