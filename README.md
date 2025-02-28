from machine import Pin
from neopixel import NeoPixel
import time


button = Pin(1, Pin.IN, Pin.PULL_UP)  
button_last_state = 1


num_pixels = 30
np = NeoPixel(Pin(7), num_pixels)


COLORS = [
    (255, 0, 0),   
    (255, 255, 0),  
    (0, 255, 0),  
    (128, 0, 128), 
    (0, 0, 255),  
    "rainbow"     
]
color_index = 0  


def wheel(pos):
    if pos < 85:
        return (pos * 3, 255 - pos * 3, 0)
    elif pos < 170:
        pos -= 85
        return (255 - pos * 3, 0, pos * 3)
    else:
        pos -= 170
        return (0, pos * 3, 255 - pos * 3)

def rainbow_cycle():
 """ 无限彩虹模式，按下按钮立即切换 """
    j = 0
    while True:
        if button.value() == 0:
            return
        for i in range(num_pixels):
            pixel_index = (i * 256 // num_pixels) + j
            np[i] = wheel(pixel_index & 255)
        np.write()
        j = (j + 1) % 256
        time.sleep_ms(10)

def wave_effect(color):
    """ 炫酷渐变流光 """
    for j in range(10):
        if button.value() == 0:
            return
        for i in range(num_pixels):
            np.fill((0, 0, 0))
            for k in range(5):
                if i - k >= 0:
                    np[i - k] = (max(0, color[0] - k * 20), max(0, color[1] - k * 20), max(0, color[2] - k * 20))
            np.write()
            time.sleep_ms(30)

def breathing_effect(color):
    """ 呼吸灯 """
    for i in range(50):
        if button.value() == 0:
            return
        np.fill((color[0] * i // 50, color[1] * i // 50, color[2] * i // 50))
        np.write()
        time.sleep_ms(10)
    for i in range(50, -1, -1):
        if button.value() == 0:
            return
        np.fill((color[0] * i // 50, color[1] * i // 50, color[2] * i // 50))
        np.write()
        time.sleep_ms(10)

while True:
if button.value() == 0 and button_last_state == 1:
        color_index = (color_index + 1) % len(COLORS)
        print("切换颜色:", COLORS[color_index])
        
        while button.value() == 0:  
            time.sleep_ms(10)

        current_color = COLORS[color_index]

        if current_color == "rainbow":
            rainbow_cycle()
        else:
            wave_effect(current_color)
            breathing_effect(current_color)
            np.fill(current_color)
            np.write()

    button_last_state = button.value()

