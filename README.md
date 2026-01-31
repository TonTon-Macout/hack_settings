# Нелегальные хаки [Settings](https://github.com/GyverLibs/Settings) 
Вмешательство во внешний вид вебморды, добавление нового функционала etc.

------------
## Для самых маленьких 

> ❓ **Tip**: Как же мне скрыть *"название части интерфейса"*? 
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

> 💡  **Tip**: По аналогии скрыть можно любую часть веб интерфейса. Чтобы найти нужный элемент — в браузере наведи мышку на нужный элемент и нажми "Проверить" или похожиый пункт.  
> 💡  **Tip**: Кроме того, что можно скрыть любой элемент, его можно и изменить (добавить оступы, убрать отсупы, поменять цвет и.д.). Так как Алекс Гайвер при разработке библиотеки не рассчитывал на любителей перетряхивать кишки, то не всегда есть возможность обойтись простым css селекоторм, иногда нужно с ними поприседать и побегать с бубном ~~(спросить думателя)~~,  но в конечном итоге изменить можно все. 


---------------
## Хочу больше, что делать? 

> ℹ️ **Tip**: Кроме изменения существующих элементов, можно добавлять свои, преумножая функционал Settings. Вот пример добавления всплывающих подсказок.
<img width="485" height="92" alt="Снимок экрана 2026-01-31 160530" src="https://github.com/user-attachments/assets/8ac00ebf-ee3d-4496-9dac-f63e61c0fd52" />

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

> ❗ **Tip**: Данный способ имеет ограничение, для того чтобы не испортить визуальное оформление, виджеты должны быть в группе и не в строке
> 
> ⛔ так работать не будет: ```cpp {sets::Row r(b); ...код виджетов}```
> 
> ✅ надо так: ```cpp {sets::Group g(b, ""); ...код виджетов}```
> 
> 📋 оставим добавление поддержки подсказок в виджетах  в Row, в отдельно стоящих и в других специфисных случаяв в качестве домашнего задания

