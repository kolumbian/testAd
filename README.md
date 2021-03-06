  1.   Открыв сайт ​https://sd.ua/ реклама сразу бросилась в глаза,
     она была везде, кроме футера.

  2.   С помощью DevTools я начал просматривать код и искать в тегах схожие
     атрибуты, чтобы попросту скрыть всё по какому-то одному атрибуту:
     class не подходит, потому что к классам с рекламой принадлежали
     и внутренние ссылки;
     href не подходит, потому что нет никакой согласованности по путям:
     -внутренние ссылки: sd.ua/;  www.sed.lg.ua/ (тот же сайт, но другой домен); https://sd.ua/.
     -рекламные ссылки: ТАКЖЕ https://sd.ua/ и другие сайты.
     Решил что rel="nofollow" идеально подходит для этого задания.

  3.   Начал писать сниппет на jQuery. 
  3.1.  Требовалось чтобы он отрабатывал после
           полной загрузки, потому я начал так:

                 $(document).ready(function() {
                   $('a[rel="nofollow"]').hide();
                 });

   Но всё оказалось не так просто, удалялись некоторые нерекламные
   ссылки и далеко не вся реклама.
   Структура кода у рекламы слишком неодинаковая. 
   
  3.2.  После этого я решил скрыть все картинки, относящиеся к рекламе,
          в коде путь ко всем был приблизительно одинаковый:

                 $(document).ready(function() {
                   $('img[src^="/files/u"]').hide();
                   $('img[src^="/files/i"]').hide();
                 });

   Снова немного не то, удалялась одна лишняя картинка,
   плюс в правой колонке сайдбара ещё оставалсь реклама. 
   
   3.3.  Теперь я во второй ссылке уточнил путь, чтобы удалялась только
   картинка рекламы кинотеатров и решил скрыть правую половину сайда:
                 
                 <!-- Запускает сниппет после полной загрузки страницы -->
                 $(document).ready(function() {   
                     <!-- Отбирает картинки по пути с рекламными и скрывает -->           
                   $('img[src^="/files/u"]').hide(); 
                     <!-- Также отбирает и скрывает картинки, по немного другому пути -->       
                   $('img[src^="/files/i/oscar"]').hide();  
                     <!-- Скрывает правую колонку сайдбара -->
                   $('div[class="half right"]').hide();      
                 });

   Код не нагружает систему.
   
   ![image](https://user-images.githubusercontent.com/78987903/109872921-bb47e100-7c75-11eb-9174-1134553fa4f5.png)
   
   
   4. Сниппет на js.
   
   4.1. Сразу написал код, который работает по тому же принципу, что и jQuery выше:
   
                  for (let node1 of document.querySelectorAll('img[src^="/files/u"]')) {
                    node1.style.display ='none'; }
                  for (let node2 of document.querySelectorAll('img[src^="/files/i/oscar"]')) {
                    node2.style.display ='none'; }
                  for (let node3 of document.querySelectorAll('div[class="half right"]')) {
                    node3.style.display ='none'; }
                    
   4.2. Надо привязать к полной загрузке страницы, попробовал:
   
                  document.addEventListener ("DOMContentLoaded", function() {}
                  
   Undefined... попробовал ещё несколько способов и потратил много времени не понимая почему не работает,
   но всё оказалось очевидно - страница уже полностью загружена, событие уже отработало и колбэк сработает.
   Значит надо привязать ко значению readyState документа и если оно равно complete, то вызвать колбэк и 
   в другом случае пусть работает по "DOMContentLoaded":
                   
                   let callback = function(){
                   for (let node1 of document.querySelectorAll('img[src^="/files/u"]')) {
                     node1.style.display ='none'; }
                   for (let node2 of document.querySelectorAll('img[src^="/files/i/oscar"]')) {
                     node2.style.display ='none'; }
                   for (let node3 of document.querySelectorAll('div[class="half right"]')) {
                     node3.style.display ='none'; }
                 };

                 if (document.readyState === "complete" ||
                     (document.readyState !== "loading")) {
                       callback();
                 } else {
                     document.addEventListener("DOMContentLoaded", callback);
                 };
                 
  ![image](https://user-images.githubusercontent.com/78987903/109982741-dca6dc80-7d0a-11eb-8196-5e3c2fcf3981.png)
  Получилось.

