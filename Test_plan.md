# SauceDemo Test Planı
 @Tahmasib

## 1. Giriş

Bu plan Swag Labs onlayn mağaza tətbiqinin ([saucedemo.com](https://www.saucedemo.com/inventory.html)) funksional və əsas qeyri-funksional keyfiyyətinin necə yoxlanacağını müəyyən edir. Məqsəd BRD-dəki 14 funksional və 6 qeyri-funksional tələbin test case-lərlə örtülməsi və əsas axınların avtomatlaşdırılmasıdır.

| Məlumat | Dəyər |
| --- | --- |
| Test obyekti | Swag Labs veb tətbiqi (login, məhsul siyahısı, səbət, checkout, təsdiq) |
| URL | https://www.saucedemo.com/ |
| İstinad sənədləri | SauceDemo BRD v1.0;&#32; |
| Sənəd versiyası | 1.0 |

## 2. Əhatə dairəsi

Test BRD-də sənədləşdirilmiş bütün müştəri axınını əhatə edir: giriş, məhsullara baxış, çeşidləmə, səbət, checkout, təsdiq və naviqasiya.

| Modul | BRD tələbləri |
| --- | --- |
| Autentifikasiya | FR-001 – FR-003 |
| Məhsullar | FR-004, FR-005 |
| Çeşidləmə | FR-006 |
| Səbət | FR-007, FR-008 |
| Checkout (məlumat və icmal) | FR-009 – FR-012 |
| Sifarişin təsdiqi | FR-013 |
| Naviqasiya | FR-014 |
| İstifadəçiyə xas davranış | FR-004, FR-006, FR-009 |
| Qeyri-funksional | NFR-001 – NFR-006 |
| End-to-End | FR-001 – FR-014 |

**Test olunmayacaq:** saytın daxili serveri və infrastrukturu, həqiqi ödəniş emalı (alış simulyasiya olunur), mobil tətbiqlər, yük (load) və penetrasiya testləri, BRD-də olmayan funksiyalar.

## 3. Test yanaşması

Test risklə idarə olunan yanaşma ilə aparlır: əvvəlcə kritik müştəri axını (login, səbət, checkout) avtomatlaşdırılır, sonra negativ və edge ssenarilər əlavə olunur.

| Səviyyə / növ | Necə icra olunur | Alət |
| --- | --- | --- |
| Smoke | Hər build-dən sonra `@smoke` tag-lı test case-lər | Cucumber + JUnit |
| Funksional (pozitiv/negativ) | BRD tələblərinə görə ssenarilər | Selenium WebDriver, Page Object Model |
| Edge / sərhəd | Uzun dəyərlər, boşluqlar, Unicode, yuvarlaqlaşdırma | Scenario Outline + data-driven |
| End-to-End | Login-dən logout-a tam alış axını | Cucumber |
| Regression | Hər release-dən əvvəl bütün avtomatlaşdırılmış test case-lər | Maven + CI |
| Cross-browser (NFR-004) | Chrome, Firefox, Edge-də əsas axın | Selenium (parametrləşdirilmiş browser) |
| Əlçatanlıq və istifadə rahatlığı (NFR-002, NFR-005) | Klaviatura ilə keçid, əlçatan adlar | Əl ilə + qismən avtomatlaşdırma |
| Explorator | `problem_user`, `visual_user` kimi xüsusi istifadəçilər | Əl ilə |

**Test dizayn texnikaları:** ekvivalent bölgülər, sərhəd dəyərlər (məsələn, 1 və 6 məhsul, 7.99 və 9.99 qiymətləri ilə vergi yuvarlaqlaşdırması) və qərar cədvəli (login nəticələri).

**Avtomatlaşdırma strategiyası:** 103 test case-dən 93-ü tam, 9-u qismən avtomatlaşdırılır, 1-i əl ilə yoxlanılır. Hər ssenari müstəqildir və login-i özü edir; sabit gözləmələr (`Thread.sleep`) yerinə explicit wait istifadə olunur (xüsusən `performance_glitch_user` üçün). Avtomatlaşdırılmış ssenarilər Page Object Model ilə yazılır: `LoginPage`, `InventoryPage`, `CartPage`, `CheckoutInfoPage`, `CheckoutOverviewPage`, `ConfirmationPage`.

## 4. Test mühiti və alətlər

Testlər saytın öz ictimai demo mühitində (https://www.saucedemo.com/) icra olunur; ayrıca staging mühiti yoxdur və data hər başlanğıcında sabitdir.

| Komponent | Seçim |
| --- | --- |
| Brauzerlər | Chrome, Firefox, Edge (son versiyalar), NFR-004 üçün |
| Avtomatlaşdırma | Java, Selenium WebDriver, Cucumber BDD, JUnit |
| Build | Maven |
| Dizayn pattern | Page Object Model |
| Hesabatlama | Cucumber HTML/JSON hesabatı, fərqli brauzerlər üçün ayrı nəticə |
| Test case idarəetməsi | Excel (SauceDemo\_Test\_Cases) |
| Defekt izləmə | Layihənin istifadə etdiyi defekt aləti (məsələn, Jira) |
| Versiya nəzarəti | Git |

Chrome-da headless rejim CI üçün, görünən rejim isə debug üçün istifadə olunur.

## 5. Test datası

Bütün test istifadəçilərinin parolu `secret_sauce`-dur. Gözlənilən nəticələr sayt tərəfindən göstərilən aşağıdakı dəyərlərə əsaslanır.

| İstifadəçi | Məqsəd |
| --- | --- |
| `standard_user` | Əsas pozitiv ssenarilər |
| `locked_out_user` | Bloklanmış hesab (FR-003) |
| `problem_user` | Məlum şəkil, çeşidləmə və forma xətaları |
| `performance_glitch_user` | Yavaş login və yüklənmə |
| `error_user` | Səbət əməliyyatlarında xətalar |
| `visual_user` | Vizual fərqlər |

| Məhsul | Qiymət ($) |
| --- | --- |
| Sauce Labs Onesie | 7.99 |
| Sauce Labs Bike Light | 9.99 |
| Sauce Labs Bolt T-Shirt | 15.99 |
| Test.allTheThings() T-Shirt (Red) | 15.99 |
| Sauce Labs Backpack | 29.99 |
| Sauce Labs Fleece Jacket | 49.99 |

**Vergi:** saytda müşahidə olunan dərəcə 8%-dir (BRD-də göstərilməyib); vergi = məhsulların cəmi × 0.08, 2 onluq yerə yuvarlaqlaşdırılır. Məsələn, $39.98 üçün vergi $3.20, yekun $43.18.

**Xəta mesajları:** `Epic sadface: Username is required`, `Epic sadface: Password is required`, `Epic sadface: Sorry, this user has been locked out.`, `Error: First Name is required`, `Error: Last Name is required`, `Error: Postal Code is required`. Tam siyahı test case faylının Test Data vərəqindədir.

## 6. Giriş və çıxış meyarları

Test yalnız giriş meyarları ödəndikdə başlayır və çıxış meyarları ödəndikdə tamamlanmış sayılır.

| Meyar | Şərt |
| --- | --- |
| Giriş | BRD v1.0 təsdiqlənib; test case-lər nəzərdən keçirilib; sayt əlçatandır; test istifadəçiləri işləyir |
| Giriş | Avtomatlaşdırma framework-ü qurulub və `standard_user` ilə login testi keçir |
| Çıxış | Bütün Kritik və Yüksək prioritetli test case-lər icra olunub |
| Çıxış | Kritik və Yüksək prioritetli test case-lərin 100%-i keçib (məlum xətalar istisna) |
| Çıxış | Açıq Kritik və ya Yüksək severity defekt yoxdur |
| Çıxış | Bütün BRD tələbləri ən azı bir test case ilə örtülüb |
| Dayandırma | Sayt əlçatan deyil və ya login bütün istifadəçilər üçün işləmir |
| Bərpa | Sayt bərpa olunub, smoke testi keçib |

`@known-issue` və `@bug-candidate` tag-lı test case-lər çıxış meyarlarından ayrıca qiymətləndirilir: onlar məlum davranışı və BRD boşluqlarını sənədləşdirir.

## 7. Risklər, fərziyyələr və asılılıqlar

Əsas risk saytın üçüncü tərəfin ictimai demo mühiti olması və BRD-də bəzi davranışların müəyyən edilməməsidir.

| Risk | Təsir | Tədbir |
| --- | --- | --- |
| Sayt əlçatmazdır və ya dəyişir | Testlər uğursuz olur | Test nəticələrini təkrar işlət, dəyişiklikləri qeyd et |
| BRD-də boş səbətlə checkout, boşluq dəyərləri və poçt indeksi formatı müəyyən edilməyib | Gözlənilən nəticə qeyri-müəyyəndir | Business Analyst ilə dəqiqləşdir, `@bug-candidate` tag-ı ilə işarələ |
| `performance_glitch_user` gecikməsi | Qeyri-sabit (flaky) testlər | Explicit wait, timeout-u artır |
| `problem_user` və `error_user` məlum xətalarla işləyir | Test nəticələri qarışa bilər | Ayrıca `@known-issue` tag-ı, çıxış meyarlarından ayrı hesabat |
| Brauzer versiyaları və WebDriver uyğunsuzluğu | Cross-browser testləri uğursuz olur | WebDriverManager və ya sabit driver versiyası |
| Sessiya/state paylaşılması (səbət brauzer yaddaşında saxlanılır) | Testlər bir-birinə təsir edir | Hər ssenaridən əvvəl yeni brauzer sessiyası və `Reset App State` |

**Fərziyyələr:** vergi dərəcəsi 8%-dir; test istifadəçilərinin parolu dəyişməyəcək; sayt ingilis dilində qalır.

**Asılılıqlar:** internet bağlantısı, brauzerlərin və driver-lərin quraşdırılması, BRD üzrə Business Analyst cavabları.

## 8. Rollar və təchizat

Test əsasən QA Automation Engineer tərəfindən aparlır; BRD sualları Business Analyst-ə yönəldilir.

| Rol | Məsuliyyət |
| --- | --- |
| QA Automation Engineer | Test planı, test case-lər, avtomatlaşdırma, icra, hesabat |
| Business Analyst | BRD açıq məsələlərinin cavablandırılması, tələblərin təsdiqi |
| Layihə rəhbəri | Çıxış meyarlarının təsdiqi, release qərarı |

**Təchizat (deliverables):** bu test plan, 103 test case (Excel və Word), Cucumber `.feature` faylları və Page Object sınıfları, icra hesabatı, defekt siyahısı, yekun test xülasəsi.

## 9. Cədvəl və mərhələlər

Plan 4 həftə üçün təxmini cədvəldir; düzən təqvim tarixləri komanda ilə təsdiqlənməlidir.

| Mərhələ | Müddət | Nəticə |
| --- | --- | --- |
| Analiz və plan | Həftə 1 | BRD təhlili, test plan, BRD sualları |
| Test case dizaynı | Həftə 1–2 | 103 test case, test data |
| Framework və smoke avtomatlaşdırma | Həftə 2 | POM strukturu, `@smoke` ssenariləri |
| Funksional avtomatlaşdırma | Həftə 2–3 | Pozitiv, negativ, edge ssenariləri |
| Cross-browser və qeyri-funksional | Həftə 3 | Chrome, Firefox, Edge nəticələri; əlçatanlıq yoxlaması |
| Regression və hesabat | Həftə 4 | İcra hesabatı, defekt siyahısı, yekun xülasə |

## 10. Defekt idarəetməsi və metriklər

Hər defekt test case ID-si, addımları, faktiki və gözlənilən nəticə, brauzer və ekran görüntüsü ilə qeyd olunur.

| Severity | Təsvir | Nümunə |
| --- | --- | --- |
| Kritik | Əsas axın işləmir | Login və ya checkout tamamlanmır |
| Yüksək | Vacib funksiya yanlışdır | Yekun məbləğ düzgün hesablanmır |
| Orta | Funksiya qısmən işləyir | Sort seçimi səbət nəticəsini pozur |
| Aşağı | Kosmetik və ya az təsirli | Mətn və ya görünüş fərqi |

**Metriklər:** icra olunan test case % (hədəf 100%), keçən test case %, açıq defektlərin severity üzrə sayı, BRD tələblərinin örtük % (hədəf 100%), avtomatlaşdırma örtüyü (hal-hazırda planlaşdırılan: 93 tam + 9 qısmən / 103). Say göstəriciləri test case Excel faylının Summary vərəqindən götürülür.

