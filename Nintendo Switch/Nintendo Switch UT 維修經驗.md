電解電容 - 有極性，量電壓
電阻 - 防止發電二極體燒毀

接點復活劑

1.8 mm 螺絲起子
外殼 4 顆 Y 字螺絲
上 1 下 2 左右 1 十字螺絲
sd 卡槽鑑 1 顆十字螺絲
金屬殼 6 螺絲

# 正面
電池左下 進水標籤 旁邊 電源管理模組
充電電壓 0.46A

USB 左方有主機板編號
HAC-CPU-01 初代主板

主機左下角第 2 條線 - 背光線

風扇 - 臺達生產，工作電壓電流為 5V/0.33A

內部記憶體 - 附在主機板上的 eMMC 儲存板，上面的 NAND 顆粒來自東芝，單顆容量為 32GB

紅框： NVIDIA ODNX02-A2，或為 Tegra X1 SoC
橙框：兩塊 16Gb 的三星 K4F6E304HB-MGCH LPDDR4 DRAM 顆粒，共計 4GB
黃框：博通 / Cypress BCM4356 802.11ac 2×2 和藍芽 4.1 整合晶片
綠框：兩個美信 MAX77621AEWI+T 三相供電模組
藍框：M92T36 630380 用途不明

# 背面
紅色：意法半導體 STM32 MC
橙色：B1913 GCBRG HAC STD T1056719A1
黃色：美信 MAX77620H EWJ 1914 KVLP (PMIC)
綠色：M92T36 PMIC
藍色：德州儀器 BQ24193 電池管理晶片

紅框：美國百利通半導體的 PI3USB30532 USB3.0/DP1.2 晶片，USB 上方
橙框：Realtek ALC5639 音訊解碼晶片
黃框：美信 Maxim MAX77620AEWJ+T 電源管理 IC
綠框：B1633 GCBRG HAC STD T1001216 的晶片，GCBRG HAC STD, Game Card Basic Read Guide Handheld And Console Suspend To Disk

量短路
紅表棒接地

開機排線
第 1 腳開機，正常有 4 V
第 1、2 腳短接 - 開機
第 3、4 腳音量，未開機沒有電壓，開機 1.8 V

開機基本
液晶螢幕、開機線、電源線

焊板 - MAX77286

二極管 - 正向有阻值，反向沒有阻值

# IC 腳位縮寫功能
COMP -  誤差放大器輸出端，外接有電容、電阻，主要是改善誤差放大器的增益和頻率特性

ISENSE - 電流檢測輸入端

RT/CT - 內部振盪器工作頻率，外部的電容、電阻決定時間常數

VFB - 反饋電壓輸入端
VREF - 參考
VCC - 電壓輸入端