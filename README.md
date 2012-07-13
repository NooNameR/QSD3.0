In case anyone other than me reads this. There are a lot of things to do. Adding
all neccessary drivers from older kernels and adapting the interfaces. This is
just a list of things I noticed or is not important to fix now.

Current kmsg output: https://gist.github.com/2d0c673ae9b863e88b0d

If you stumple over this and are interested to help then feel free to contact me.

I won't guarantee that I continue to work on this.

### Things to check
 * BUG() seems to hang the device and pulling the battery is neccessary
   Only in the early boot phase? WARNING also affected?
 * acpuclk_init() does not differentiate between GSM and CDMA devices yet
 * Is adding smd_set_channel_list() the correct way now?
   There are two smd_tty drivers. One under drivers/tty/serial which seems to be old
   and currently used one. A newer driver is under arch/arm/mach-msm. At the moment
   both will init and clash. The new driver uses smd_platform to define stuff. I 
   don't understand that yet.
 * USB stuff looks very different
   * board_serialno() ?
 * Microp? Whats that? Seems to be disabled in tiamat
 * are there versions of the msm_{enable|disable|read}_fast_timer or is adding
   them the correct way? What about GLOBAL_TIMER as argument for msm_read_timer_count?
 * MSM_RPC_VERS macro
 * clk_prepare_enable instead of just clk_enable in i2c-msm, msm_sdcc!?
   Where is the correct place to prepare clocks?
 * enabling the regulator tpm6503 driver hangs boot
   Traced it down to i2c_lock_adapter in i2c_transfer over print_constraints _regulator_get_voltage so far
   Either rt_mutex_lock does not work or adapter is already locked. Latter does not seem the case.
 * Error about "dev_init_debugfs: Error-Bad Function Input" seems to be unimportant (in the dummy case)
 * gpio_tlmm_config(0x0002c390, GPIO_CFG_ENABLE) <?> failed: -5
   pin 57 func 0 dir 1 pull 1 drvstr 1
   caused by bt_gpio_table (BRAVO_GPIO_BT_WAKE)
 * Enabling pm causes the system to hang
   Hangs inside msm_timer_enter_idle
   Also last_kmsg does not exist after reboot for some reason