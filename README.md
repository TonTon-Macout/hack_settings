## Нелегальные хаки [Settings](https://github.com/GyverLibs/Settings) 
Вмешательство во внешний вид вебморды, добавление нового функционала etc.

- Подразумевается, что вы уже умеете работать с библиотекой [Settings](https://github.com/GyverLibs/Settings), а так же желательны хотябы поверхностные знания CSS и JavaScrypt
- Примеры не всегда имеют уневерсальное применение, иногда к конкретной задаче, конкретному расположению элементов - необходим индивидуальный подход


### Оглавление 
- [скрываем элементы  ↓](#hide)
- [добавляем всплывающие подсказки к лейблам  ↓](#tooltips)
- [своя кастомная тема для всей вебморды  ↓](#theme)
- [подключение javascript, css, шрифтов  ↓](#innards)

  
------------
<h2 id="hide" >Для самых маленьких</h2>

> ❓ **Tip**: Как же мне скрыть *"название части интерфейса"*?
> 
> 💡  **Tip**: Воспользуемся html виджетом и подбором css селекторов
<details>
<summary>показать код</summary>
    
#### этот код скрывает от пользователя файловую систему и кнопку загрузки ОТА 
    
```cpp
const char user_mode_css[] PROGMEM = R"raw(
<span class="user-mode"></span>
<style>
    .widget:has(.user-mode){display:none;!important;}/*скрываем виджет со стилями*/
    div.fs_row{display:none;}/*скрываем файловую систему*/
    .menu_icon:has(.cloud){display:none !important;} /*скрываем иконку ота*/
</style>
)raw";

static bool user_mode = false;
{   sets::Group  g(b, ""); 
    if(!user_mode) b.HTML("", user_mode_css);
    if(b.Switch("Режим разработчика", &user_mode)) b.reload();  
}

```

</details>
    
> 💡  **Tip**: По аналогии скрыть можно любую часть веб интерфейса. Чтобы найти нужный элемент — в браузере наведи мышку на нужный элемент и нажми "Проверить" или похожый пункт.  
> 💡  **Tip**: Кроме того, что можно скрыть любой элемент, его можно и изменить (добавить оступы, убрать отсупы, поменять цвет и.д.).
>
> ℹ️ Так как Алекс Гайвер при разработке библиотеки не рассчитывал на любителей перетряхивать ки́шки, то не всегда есть возможность обойтись простым css селекоторм, иногда нужно с ними поприседать и побегать с бубном ~~(спросить думателя)~~,  но в конечном итоге изменить можно все. 


---------------
<h2 id="tooltips" >Хочу больше, что делать?</h2>

> ℹ️ **Tip**: Кроме изменения существующих элементов, можно добавлять свои, преумножая функционал Settings. Вот пример добавления всплывающих подсказок.
<img width="485" height="92" alt="Снимок экрана 2026-01-31 160530" src="https://github.com/user-attachments/assets/8ac00ebf-ee3d-4496-9dac-f63e61c0fd52" />

<details>
<summary>показать код</summary>

#### этот код добавляет всплывающие подсказки (tooltip'ы) к лейблам 
    
```cpp
const char tooltip_css[] PROGMEM = R"raw(
<span class="tooltip-main"></span>
<style>
.widget:has(.tooltip-main) {
  display:none;
}
.widget:has(.tooltip) {
  height: 0;
  margin-top: -1px;
  margin-bottom: -1px;
  padding: 0;
}
.widget:has(.tooltip) + .widget .widget_label {
  text-decoration: underline from-font dotted var(--accent);
  cursor: help;
}
.tooltip {
  position: absolute;
  left: 0;
  top: 100%;
  pointer-events: none;
}
.tooltip::after {
  content: attr(data-tooltip);
  position: absolute;
  bottom: 100%;                  
  left: 10px;    
  background: var(--accent);
  color: var(--font);
  border: 1px solid var(--font);
  padding: 6px 10px;
  border-radius: 4px;
  font-size: 12px;
  white-space: normal;
  overflow-wrap: break-word;
  width: max-content;
  max-width: 350px;
  text-wrap: balance;
  z-index: 1000;
  opacity: 0;
  transition: opacity 0.5s cubic-bezier(0, 0.69, 0.79, 0.88);
}
.tooltip::before {
  content: "";
  position: absolute;
  top: 0;                
  left: 20px;             
  margin-top: 0px;               
  border-left: 8px solid transparent;
  border-right: 8px solid transparent;
  border-top: 8px solid var(--font); 
  z-index: 1999;
  opacity: 0;
  transition: opacity 0.2s cubic-bezier(0, 0.69, 0.79, 0.88);
}
.widget:has(.tooltip):has(+ .widget .widget_label:hover) .tooltip::after,
.widget:has(.tooltip):has(+ .widget .widget_label:hover) .tooltip::before {
  opacity: 1.0;
}
.widget {
  position: relative;
}
</style>
)raw";
static bool sound = true;
static bool button_sound = true;
static bool cucko_sound = true;
static bool charging_sound = true;
static bool thunder_sound = true;
b.HTML("", tooltip_css);
 
b.HTML("", "<span class=\"tooltip\"  data-tooltip=\"Включить/выключить звук\"></span>");
if (b.Switch("Звук", &sound))b.reload();
        
if (b.Switch("Звук кнопок", &button_sound))b.reload();
        
if (b.Switch("Звук кукушки", &cucko_sound))b.reload();
        
b.HTML("", "<span class=\"tooltip\"  data-tooltip=\"Звук уведомления о начале зарядки\"></span>");
if (b.Switch("Звук зарядки", &charging_sound))b.reload();
       
b.HTML("", "<span class=\"tooltip\"  data-tooltip=\"Звук уведомления о молнии\"></span>");
if (b.Switch("Звук молнии", &thunder_sound))b.reload();

```  
</details>

> ❗ **Tip**: Данный способ имеет ограничение, для того чтобы не испортить визуальное оформление, виджеты должны быть в группе и не в строке
> 
> ⛔ так работать не будет: ```cpp {sets::Row r(b); ...код виджетов}```
> 
> ✅ надо так: ```cpp {sets::Group g(b, ""); ...код виджетов}```
> 
> 📋 оставим добавление поддержки подсказок в виджетах  в Row, в отдельно стоящих и в других специфисных случаях - в качестве домашнего задания



---------------
<h2 id="theme" >А если я любитель чего-то особенного?</h2>

> ℹ️ **Tip**: Будем менять Settings до неузноваемости!
>
> На этот раз воспользуемся функционалом кастомного виджета 

<details>
<summary>показать код</summary>

#### Этот код обратимо преобразует весь внешний вид веб интерфейса, делая его действительно красивым. Это полноценный пример, можно просто скопировать загрузить в есп, незабыв заполнить данные wifi
    
```cpp
#include <Arduino.h>

#define WIFI_SSID ""
#define WIFI_PASS ""

#ifdef ESP8266
#include <ESP8266WiFi.h>
#else
#include <WiFi.h>
#endif

#include <SettingsGyver.h>
SettingsGyver sett("SETTINGS THEME");
sets::Logger logger(2000);

char ssid[25] = WIFI_SSID;
char password[20] = WIFI_PASS;

bool show_setting, timer, process, connected, my_theme = true;
uint8_t mode, dimm;
uint32_t timer_time = 64800;  // время будильника
uint32_t start_mute = 50000;
uint32_t end_mute = 60000;

void build(sets::Builder &b) {
  BSON p;
  if (my_theme) b.Custom("theme", 4477, p);

  { sets::Group r(b);
    if (b.Switch("Switch")) {
      b.reload();
    }
    if (b.Switch("Switch")) {
      b.reload();
    }
    if (!process) {
      if (b.Button(F("пуск"))) {
        process = !process;
        b.reload();
      }
    } else {
      if (b.Button(F("стоп"))) {
        process = !process;
        b.reload();
      }
    }
  }

  { sets::Group r(b);

    if (b.Select("mode", "0;1;2;3;4;5", &mode))  b.reload();
  }


    { sets::Group r(b);
      if (b.Switch("Время", &timer))  b.reload();
      if (timer) b.Time("установить", &timer_time);
      if (timer){
        sets::Row g(b);
        if (b.Time("начало", &start_mute)) ;
        if (b.Time("конец", &end_mute)) ;
      }
    }

    // Расширенные Настройки
    { sets::Group r(b, "");
      if (b.Switch("РАСШИРЕННЫЕ НАСТРОЙКИ", &show_setting)) b.reload();
      
      if (show_setting) {
        if (b.Slider("слайдер", 1, 10000, 1, ""));
        if (b.Switch("Switch"));
        if (b.Slider("яркость", 0, 255, 1, "", &dimm));
        if (b.Switch("сделать красиво", &my_theme)) b.reload();
      }
    }

    // логгер
    if (show_setting) {
      sets::Group r(b, "ЛОГГЕР");
      b.Log(logger);
      {
        sets::Buttons r(b);
        if (b.Button("Очистить"))  //
        {
            logger.clear();
            b.reload();
        }

              if (b.Button("Инфо")) {
        static uint8_t count_ = 0;
        count_++;


                        logger.print( "------------ STATUS ");
                        logger.print(count_);
                        logger.println( " ------------");
                        logger.print("wifi RSSI: ");
                        logger.println(WiFi.RSSI());
                        // Вывод информации о памяти
                        logger.println( "--- оперативная память ---");
                        logger.println( "Свободная куча: " + (String)ESP.getFreeHeap() +" байт");
                        logger.println( "Общий размер кучи: " + (String)ESP.getHeapSize() + " байт");
                        logger.println( "Минимальная свободная куча: "+ (String)ESP.getMinFreeHeap()+ " байт");
                        //  logger.println( "Максимальный блок кучи (Max Alloc Heap): ", ESP.getMaxAllocHeap(), " байт");
                        logger.println( "--- флеш память ---");
                        logger.println( "Общий размер флэш-памяти : "+ (String)ESP.getFlashChipSize()+" байт");
                        logger.println( "Размер скетча : "+ (String)ESP.getSketchSize()+ " байт");
                        logger.println( "Свободное место для скетча: "+ (String)ESP.getFreeSketchSpace()+ " байт");
                        logger.println( "Частота флэш-памяти (Flash Chip Speed): "+ (String)ESP.getFlashChipSpeed()+ " Гц");
                        logger.print( "Режим флэш-памяти (Flash Chip Mode): ") ;
                        logger.print(ESP.getFlashChipMode() == FM_QIO ? "QIO" : ESP.getFlashChipMode() == FM_QOUT ? "QOUT"
                                                                                                                           : ESP.getFlashChipMode() == FM_DIO    ? "DIO"
                                                                                                                           : ESP.getFlashChipMode() == FM_DOUT   ? "DOUT"
                                                                                                                                                                 : "UNKNOWN");

                        logger.println( "--- внешняя память ---");
// Вывод информации о PSRAM (если поддерживается)
#ifdef CONFIG_SPIRAM_SUPPORT
                        logger.println( "Общий размер PSRAM (PSRAM Size): "+ (String)ESP.getPsramSize()+ " байт");
                        logger.println( "Свободный PSRAM (Free PSRAM): "+ (String)ESP.getFreePsram()+ " байт");
#else
                        logger.println( "PSRAM: Не поддерживается на этом модуле");
#endif

                        logger.println( "--- процессор ---");
                        // Вывод информации о процессоре
                        logger.println( "Частота процессора: "+ (String)ESP.getCpuFreqMHz()+ " МГц");
                        logger.println( "Количество ядер: "+ (String)ESP.getChipCores());
                        logger.println( "Модель чипа: "+ (String)ESP.getChipModel());
                        logger.println( "Ревизия чипа: "+ (String)ESP.getChipRevision());
                        logger.println( "Версия SDK: "+ (String)ESP.getSdkVersion());

        b.reload();
      }

      }

    }

    if (show_setting) {
      sets::Group r(b, "WIFI");
      if (b.Input("имя", AnyPtr(ssid, 25)));
      if (b.Pass("пароль", AnyPtr(password, 20)));
    }

    {
        sets::Buttons r(b);
        if (b.Button("Перезагрузить", 0xEB5453)) ESP.restart();

        if (b.Button("Обновить"))  b.reload();
        
    }

    {sets::Row r(b);
      if (connected) b.Label(F(""), "хороший статус", 0x02b314);
      else b.Label(F(""), "плохой статус", sets::Colors::Red);

      if (!connected) {
        if (b.Button(F("клац"))) {
          connected = !connected;
          b.reload();
        }
      }
    }
}
const char theme[] PROGMEM = R"raw(
class theme extends WidgetBase {
    constructor(data) {
        super(data);
        super.addOutput(Component.make('style', {
            context: this,
            var: 'out',
            id:  'uniqueStyle',
           
        }));
        this.update(data);
    }
    update(data) { this.$out.innerHTML = `
    /* ----------------------------------------------------------------------------*/  

/* ============================================== */
.HR {
    background-color: var(--back);
    margin: -12px -10px;
    padding: 0px 10px;
    display: flex;
    height: 14px;
}

/* ============================================== */
/* ИСПРАВЛЕНИЯ СТИЛЕЙ СЕТТИНГС */
div.main > div.main_col >div.page> div.buttons>.button:nth-child(1) {
    border-radius: 10px 0 0 10px;
    border-right: solid 2px var(--back);
}
body > div.main > div.main_col >div.page> div.buttons {
    padding: 6px 0px;
    margin: 35px 11px 6px 11px;
}
div.main > div.main_col >div.page> div.buttons>.button:nth-child(2) {
    border-radius: 0px 10px 10px 0px;
    border-left: solid 2px var(--back);
}
body > div.main > div.main_col >div.page> div.buttons>.button {
    margin: 0px;
    width: 100%;
    padding: 10px 10px;
}
.nav {
    font-family: sans-serif;
    font-optical-sizing: auto;
    font-weight: 500;
    font-style: normal;
    color: var(--accent);
    margin-left: 10px;
    filter: brightness(2.8) grayscale(1);
 }
/* запретить */

.log .info { /* текст уровня инфо в логгере*/
    color: var(--accent);
}
.log p { /* просто текст в логгере*/
    color: var(--accent);
    max-height: 500px;
}
/* ДЛЯ ТЕМНОЙ ТЕМЫ */
/* добавить -- body.theme_light.theme_dark  */
 
/* названия групп */
body.theme_light.theme_dark .group_title span {
    color: var(--accent);
    filter: brightness(0.8); /* уменьшаем яркость */
    padding-left: 20px;
}

/* ДЛЯ СВЕТЛОЙ ТЕМЫ */
/* добовляем --  .theme_light:not(.theme_dark)  */ 

/* общие цвета */
body.theme_light:not(.theme_dark) {
    /* --accent: #5837a9 !important; */
 }

body.theme_light:not(.theme_dark) .group_col>.buttons:last-child:not(:only-child) {
    border-radius: 0px 0px 15px 15px;
}
/* текст в логере уровня инфо */
body.theme_light:not(.theme_dark) .log .info {
    color: #37483b;
}
.theme_light:not(.theme_dark) {
   --dark: #8d6767;
   --back: #e5d73e;
   --tab: #4545bf;
   --font: #ffeded;
   --font_tint: #d3ffd8;
   --font_inv: #fbfbfb;
   --shadow: #000000b8;
   --shadow_light: #00180622;
   --accent: #5837a9;  
}

.theme_light:not(.theme_dark) .header {
    position: fixed;
    top: 0;
    z-index: 1000000;
    display: flex;
    justify-content: space-between;
    background: #1f1f61;
    padding: 7px 15px;
    box-sizing: border-box;
    margin: auto;
    left:0;
    /* max-width: 500px; */
    width: 100%;
    border: 5px #00ffde;
    color: #00ff8c;
    border-bottom: solid 0px rgb(90, 78, 188);
    box-shadow: 1px 0px 4px #ffffff;
}

.theme_light:not(.theme_dark) .page:first-of-type {
    margin-top: 45px;
}
.theme_light:not(.theme_dark) .menu_icons {
    margin-top: 50px;
 }
/* разделение двух горизонтальных виджетов */
.theme_light:not(.theme_dark) .group_row > .widget:nth-of-type(2):has(.widget_label) {
   border-left: 2px solid #e5d73e;
   padding-left: 10px;
 }

/* кнопки в группе  */
.theme_light:not(.theme_dark) .group_col>.button:last-child {
       box-shadow: inset 0 3px 5px #00000035;
}
.theme_light:not(.theme_dark) .group_col>.buttons:last-child:not(:only-child)>.button {
       box-shadow: inset 0 3px 5px #00000035;
}

/* названия групп */
.theme_light:not(.theme_dark)  .group_title span {
   color: #4ca93d;
   font-weight: 600;
   margin-left: 10px;
 }
/* применяется ко всем свичам */
.theme_light:not(.theme_dark) .switch {
    background-color: #3846c3;;
    border: solid 2px #453079;
    border-radius: 8px;
    height: 25px;
    width: 50px;
    box-shadow: inset 0 0px 6px rgb(0 0 0 / 37%);
}
/* переопределить для включенного свича */
.theme_light:not(.theme_dark) .switch:checked {
    background-color: #5168ff;
    box-shadow: inset 0 0px 8px 0px rgb(243 198 150 / 91%);
}

/*  выключатель выключенного свича */
.theme_light:not(.theme_dark) .switch:before {
   background: #ffffffa6;
   border-radius: 2px;
   box-shadow: 0 0px 7px rgb(0 0 0);
   height: 21px;
   left: 8px;
   position: absolute;
   /* right: -2px; */
   top: -0px;
   transition: all .15s;
   width: 10px;
   z-index: 2;
}
/*  выключатель включенного свича */
.theme_light:not(.theme_dark) .switch:checked:before {
  background: #ffffff;
  border-radius: 2px;
  box-shadow: 0 0px 9px rgb(0 0 0 / 59%);
  height: 21px;
  left: 26px;
  position: absolute;
  top: +0px;
  transition: all .15s;
  width: 10px;
  z-index: 2;
}   
/* ползунок слайдера */
.theme_light:not(.theme_dark)  input[type="range"]::-webkit-slider-thumb {
-webkit-appearance: none; 
appearance: none; 
width: 15px; 
height: 25px;
background: #ffffff;
border-radius: 4px; 
}


/* текст первого виджета в группе */
.theme_light:not(.theme_dark)   .group>.group_col>.widget:first-of-type>.widget_row>div>.widget_label:first-of-type {
      font-weight: bold;
      color: #efd4d4;
      text-shadow: 5px 2px 5px #0c1d5938, -5px -1px 10px #242a4b24, 0px 0px 10px #0c102b4f, 0px 0px 20px #1b1c3100;
}  
/* одинокая группа кнопок */
 .theme_light:not(.theme_dark) > div.main > div.main_col >div.page> div.buttons>.button:nth-child(2) {
      border-radius: 0px 10px 10px 0px; 
      border-left: solid 2px #e5d73e;
 } 
 .theme_light:not(.theme_dark) > div.main > div.main_col >div.page> div.buttons>.button:nth-child(1) {
      border-radius: 10px 0 0 10px; 
      border-right: solid 2px #e5d73e;
 } 

 /* статус */
/* должен быть в ROW */
 .page>.row>.group_row:last-of-type {
         box-shadow: none;
         margin: 0 10px;
         background: transparent;
         justify-content: center;
         color: #2e8f18;
 }


     `;
    }
}

)raw";

void setup() {
  Serial.begin(115200);
  Serial.println();

  // ======== WIFI ========
  WiFi.mode(WIFI_AP_STA);
  WiFi.begin(ssid, password);
  uint8_t tries = 20;
  while (WiFi.status() != WL_CONNECTED) {
    delay(500);
    Serial.print(".");
    if (!--tries) break;
  }
  Serial.println();
  if(WiFi.status() == WL_CONNECTED) {
      Serial.print("WIFI Connected: ");
  Serial.println(WiFi.localIP());
  }
  else {
    Serial.print("WIFI Connect error!");
  }


  // ======== SETTINGS ========
  sett.begin();
  sett.onBuild(build);
  sett.setCustom(theme, strlen_P(theme), false);

}

void loop() {
  static unsigned long prev = 0;
  static bool prevConn = false;
  
  if (millis() - prev >= 5000) {
    prev = millis();
    connected = false;
    
    if (connected != prevConn) {
      sett.reload();
      prevConn = connected;
    }
  }

  sett.tick();
}
```
</details>


------------
<h2 id="innards" >Для тех кто желает по настоящему глубокого проникновения в сеттингс</h2>

