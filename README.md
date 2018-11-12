# MVVM-and-Dependency-Injection-

<p dir='rtl' align='right'>
در swift میتوان برای پارامترهای یک تابع ، مقادیر پیش فرض تعریف کرد. مثال زیر را ببینید: </p>
  
    func print(_ string: String, options: String? = nil) { ... }

    func showToast(view: UIView, message: String, duration: TimeInterval = 3.0, backColor: UIColor = UIColor.black.withAlphaComponent(0.8)) { ... }

<p dir='rtl' align='right'>
اگر بخواهیم dependency injection  داشته باشیم چطور؟ به مثال زیر نگاه کنید:
  </p>

    class TicketsViewModel {

        let service: TicketService
        let database: TicketDatabase

        init(service: TicketService, database: TicketDatabase) { ... }
    }

<p dir='rtl' align='right'>
همانطور که میدانید وقتی یک پروژه در xcode ایجاد میکنید، در پنجره ای که میخواهید optionهای پروژه شامل نام پروژه، تیم و زبان برنامه نویسی و … را مشخص کنید در قسمت انتهایی اگر گزینه های Unit test و UI test را انتخاب کنید، سه Target برای شما ایجاد میشود. یکی که همان اپلیکیشن شماست و همنام با آن است و دو تای دیگر target های مربوط به تستها می باشند.
خب، حالا اگر بخواهیم از کلاس TicketsViewModel در پروژه اصلی و همچنین در تست ها استفاده کنیم، چگونه میتوانیم این کار را انجام دهیم؟
  </p>
  
<p dir='rtl' align='right'>
نحوه استفاده در App target بدین صورت است:
  </p>
  
    let model = TicketsViewModel(service: LiveTicketService(), database: LiveTicketDatabase())

<p dir='rtl' align='right'>
نحوه استفاده در Test target بدین صورت است:
  </p>
  
    let model = TicketsViewModel(service: MockTicketService(), database: MockTicketDatabase())

<p dir='rtl' align='right'>
همانطور که قابل تشخیص است LiveTicketService و MockTicketService دو پیاده سازی مختلف از پروتکل  TicketService هستند . همچنین LiveTicketDatabase و MockTicketDatabase دو پیاده سازی مختلف از پروتکل TicketDatabase هستند.
</p>
<p dir='rtl' align='right'>
همانطور که دیدیید در مثال بالا dipendency بین کلاس TicketsViewModel و پورتکلهای سطح بالای TicketService و TicketDatabase تعریف شده است. اگر dependency بین کلاس TicketsViewModel و  LiveTicketService (که یک پیاده سازی سطح پایین یا concrete از پروتکل TicketService است) تعریف میشد در target تست به مشکل برمیخورید و با خطای کامپایلر  روبرو میشدید چون تست شما از LiveTicketService اطلاعی ندارد، یعنی بهتر است و توصیه میشود که اطلاعی نداشته باشد. 
</p>
<p dir='rtl' align='right'>
بنابراین هنگام استفاده از dependency injection در view modelها در معمارری MVVM باید dependency ها یا همان وابستگی ها در بالاترین سطح انتزاع تعریف شوند (یعنی در سطح پروتکل های abstract). در این صورت میتوانیم هر پیاده سازی دیگری را به کلاس TicketsViewModel، مرتبط کنیم. اما اگر LiveTicketService را بعنوان پارامتر پیش فرض به کلاس TicketsViewModel وابسته میکردیم (inject میکردیم)، از آنجایی که LiveTicketService یک نوع سطح پایین (concrete type) است، در استفاده های بعدی دچار مشکل میشدیم مثلا در تست وقتی که میخواهیم یک پیاده سازی دیگری (یعنی MockTicketService) را به TicketsViewModel وابسته یا inject کنیم.
</p>
<p dir='rtl' align='right'>
بنابراین این نکته را به خاطر داشته باشید که همیشه برای dependency injection در کلاسها از بالاترین سطح انتزاع استفاده کنید. 
</p>
