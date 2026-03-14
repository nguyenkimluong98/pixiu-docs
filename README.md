# Mục lục:
1. [Level](#level)
2. [Advertisement](#advertisement)
3. [InApp](#inapp)
4. [Funnel](#funnel)


# Level

## I. Tứ phân vị

Tứ phân vị (quartiles) là một khái niệm trong thống kê, được dùng để chia một tập hợp dữ liệu thành bốn phần bằng nhau về số lượng quan sát. Mỗi phần đại diện cho 25% dữ liệu.

Tứ phân vị bao gồm:

* Q1 (first quartile - tứ phân vị thứ nhất: 25%) : là giá trị mà 25% dữ liệu nhỏ hơn hoặc bằng Q1.
* Q2 (median - trung vị: 50%): chia dữ liệu làm hai phần bằng nhau, 50% nhỏ hơn hoặc bằng Q2.
* Q3 (third quartile - tứ phân vị thứ ba: 75%): là giá trị mà 75% dữ liệu nhỏ hơn hoặc bằng Q3.&#x20;

Việc sử dụng tứ phân vị ở các biểu đồ level sẽ cho cái nhìn khách quan hơn về các chỉ số phân tích so với việc chỉ xét về giá trị trung bình.

## II. Biểu đồ

### 1. Độ khó trên người <mark style="color:green;">PASS</mark> (Độ khó với người Pass)

**Định nghĩa:** Số lượt chơi <mark style="color:red;">**FAIL**</mark> của 1 người chơi để <mark style="color:green;">**PASS**</mark> qua level đó (Một người phải chơi <mark style="color:red;">**FAIL**</mark> ít nhất bao nhiêu lượt để qua level).&#x20;

**Ý nghĩa**: cho biết level nào quá khó, hoặc level quá dễ từ đó tối ưu lại để tăng trải nghiệm người dùng.&#x20;

**Công thức**: &#x20;

* Từ các bản ghi LevelLog, dựa vào trường status, ta tổng hợp được mỗi player, mỗi level có Fail bao nhiều lần, chỉ lấy các player có pass level (số bản ghi pass level của player đó>0)

$$
LevelLog -> PlayData := {Player, Level, FailCount| PassCount>0}
$$

* Từ dữ liệu trên, ta có:

$$
\begin{split}
Difficulty\_{LvX} &= \frac
{sum(PlayData.FailCount\_{Level = x})}
{count(PlayData.Player\_{Level=x})} \\\\&= \frac{Total~~Fails~~LvX\_{For~~players~~passed~~LvX} }{Total~~players~~passed~~LvX}
\end{split}
$$

* Trong đó:
  * Level : level hiện tại của người chơi
  * Fail count : số bản ghi có status = fail tương ứng với player và level đó
  * Pass count : số bản ghi có status = pass tương ứng với player và level đó

**VD**: Hình 1.1  thể hiện ở level 69, mỗi người chơi cần chơi Fail trung bình (Average) 0.43 lượt mới có thể Pass qua level 69. 25% và 50% người chơi Pass qua level ở lượt chơi đầu tiên và 75% người chơi cần trung bình 0.43 lượt để Pass qua level.

<div align="center"><figure><img width="1428" height="960" alt="Image" src="https://github.com/user-attachments/assets/bfe2a450-c0c9-4ab2-8b84-2c9322d67236" /><figcaption><i>Hình 1.1 Biểu đồ Độ khó trên người Pass</i></figcaption></figure></div>

### 2. Độ khó trên người <mark style="color:red;">FAIL</mark> (Độ khó với người chưa Pass)

**Định nghĩa:** Số lượt chơi <mark style="color:red;">**FAIL**</mark> của 1 người chơi mà <mark style="color:red;">**CHƯA PASS**</mark> qua level đó. (Người chơi chơi <mark style="color:red;">**FAIL**</mark> bao nhiêu lượt thì bỏ game/dừng game).

**Ý nghĩa**: cho biết level nào quá khó, gây nản cho người dùng

**Công thức**: &#x20;

* Từ các bản ghi LevelLog, dựa vào trường status, ta tổng hợp được mỗi player , mỗi level có Fail bao nhiều lần, chỉ lấy các player không  pass level (số bản ghi pass level của player đó = 0)

  $$
  LevelLog -> PlayData := {Player, Level, FailCount| PassCount=0}
  $$
* Từ dữ liệu trên, ta có:

$$
\begin{split}
Difficulty\_{LvX} &= \frac
{sum(PlayData.FailCount\_{Level = x})}
{count(PlayData.Player\_{Level=x})} \\\\&= \frac{Total~~Fails~~LvX\_{For~~players~~not~~passed~~LvX} }{Total~~players~~not~~passed~~LvX}
\end{split}
$$

* Trong đó:
  * Level : level hiện tại của người chơi
  * Fail count: số bản ghi có status = fail tương ứng với player và level đó
  * Pass count: số bản ghi có status = pass tương ứng với player và level đó

VD: Hình 1.2 thể hiện ở level 69, mỗi người chơi chơi Fail trung bình (Average) 3.23 lượt vẫn không thể Pass level. Có 25% người chơi chỉ chơi trung bình 0.31 lượt và dừng game, 50% người chơi chơi trung bình 1.31 lượt vẫn không thể Pass level, và 75% người chơi chơi trung bình 1.62 lượt và bỏ/dừng game ở level 69.

<div align="center"><figure><img width="1440" height="970" alt="Image" src="https://github.com/user-attachments/assets/a2ac0e3e-3532-4b3c-b807-e095a11f1ee1" /><figcaption><i>Hình 1.2 Biểu đồ Độ khó trên người chơi Fail</i></figcaption></figure></div>

### 3. Tỉ lệ người chơi Chưa Qua Level

**Định nghĩa:** Biểu đồ thể hiện tỉ lệ phần trăm lượng người chơi chưa qua level.

**Ý nghĩa**: cho biết level nào gây drop rate cao( vì khó/ vì lỗi/vì chán/...)

**Công thức**:  &#x20;

* Từ các bản ghi LevelLog, dựa vào trường status, ta tổng hợp được mỗi:level có bao nhiêu log Pass, vì mỗi player chỉ log pas 1 lần nên PassCount = số player Pass level

  $$
  LevelLog -> PlayData := {Level, PassCount}
  $$
* Từ dữ liệu trên, ta có:

$$
\begin{split}
Ratio\_{LevelX} &= \frac
{PlayData.PassCount\_{Level = x})}
{PlayData.PassCount\_{Level=x-1})}
\\\\&= \frac
{Total~~players~~passed~~Lv~~x }
{Total~~players~~passed~~Lv~~x-1}
\end{split}
$$

* Trong đó:
  * Level: level cao nhất mà người chơi đã Pass.
  * Pass count: số bản ghi có status = Pass tương ứng với level đó

**Lưu ý:** Nếu chỉ log level từ level 1 thì sẽ không có dữ liệu level 0 (người chỉ mới mở game/ mở tính năng mà chưa thực sự chơi) nên sẽ không thể có drop rate level 1, vì thế trong biểu đồ ví dụ trục level mới bắt đầu từ 2, nếu muốn có drop rate level 1 xin hãy log level 0.

**VD**: Hình 1.3 thể hiện 6.21% người chơi chưa qua level 69.

<div align="center"><figure><img width="1434" height="966" alt="Image" src="https://github.com/user-attachments/assets/e90ee59b-054c-4797-9c9a-73c5d232a8ab" /><figcaption><i>Hình 1.3. Biểu đồ Tỉ lệ người chơi chưa qua Level</i></figcaption></figure></div>

### 4. Tổng người chơi theo Level

**Định nghĩa:** Biểu đồ thể hiện phân bố người chơi theo các level

**Công thức**:  &#x20;

* Từ các bản ghi LevelLog, dựa vào trường status, ta tổng hợp được mỗi player tối đa đạt đến level nào:

  $$
  LevelLog -> PlayData := {Player, Level}
  $$
* Từ dữ liệu trên, ta có:

$$
\begin{split}
Total\_{LevelX}&= count(PlayData.Player\_ {Level=X})
\\&= Total\~Player\_{MaxPassedLv=X}\
\end{split}
$$

* Trong đó:
  * Level: level cao nhất mà người chơi đã Pass.

**Lưu ý:**&#x20;

* Người chơi A đã pass level 100, tuy nhiên muốn quay lại chơi level 5 để kiếm tài nguyên thì người chơi A vẫn được tính là đang ở level 100.
* Các người chơi Fail level đầu tiên xong bỏ game khi chưa pass sẽ bị hệ thống tính là level 0

**VD**:&#x20;

<div align="center"><figure><img width="1430" height="960" alt="Image" src="https://github.com/user-attachments/assets/558454f1-b051-449b-ac00-e40f09ac3f17" /><figcaption><i>Hình 1.4. Biểu đồ Tổng người chơi theo level</i></figcaption></figure></div>

### 5. Thời gian chơi trung bình

**Định nghĩa:** Biểu đồ thể hiện thời gian trung bình của người chơi với mỗi loại status chơi&#x20;

**Ý nghĩa:** Biểu đồ cung cấp thông tin cho dev cân đối lại độ dài của level

**Công thức**:  &#x20;

* Từ các bản ghi LevelLog, ta có:

$$
\begin{split}
Time\_{LevelX,StatusY} &= \frac
{\sum LevelLog.Duration\_{Level = X, Status = Y})}
{count(LevelLog\_{Level = X, Status = Y})}
\\\\&= \frac
{Total~~duration~~players~~spent~~for~~LvX~~StatusY }
{Times~~players~~play~~LvX~~StatusY}
\end{split}
$$

* Trong đó:
  * Level: level hiện tại của người chơi
  * Status: Pass, Fail, RePlayPass, RePlayFail,...
  * Đơn vị: Giây (Second-s)

**VD**: Hình 1.5, ở level hiện tại 69, thời gian chơi trung bình để Pass qua level là 339,02 (s) và thời gian chơi Fail trung bình là 240,88 (s).

<div align="center"><figure><img width="1434" height="960" alt="Image" src="https://github.com/user-attachments/assets/13eb60af-a360-4a2a-92ad-03daf4c31fb2" /><figcaption><i>Hình 1.5 Biểu đồ thời gian trung bình</i></figcaption></figure></div>

### 6. Số lượt chơi ở level hiện tại

**Định nghĩa:** Biểu đồ thể hiện số lượt chơi của người chơi ở level hiện tại

**Công thức**:  &#x20;

* Từ bản ghi LevelLog, ta có:&#x20;

$$
\begin{split}
PlayTimes\_{LevelX}&= count(LevelLog\_{Level=X})
\end{split}
$$

* Trong đó:&#x20;

&#x20;      — Level là level hiện tại của người chơi

&#x20;     — Status: Pass, Fail &#x20;

Ví dụ: Ở hình 1.6, tại level 69, có 332 lượt chơi có status Pass và 185 lượt chơi có status Fail.

<div align="center"><figure><img width="1425" height="957" alt="Image" src="https://github.com/user-attachments/assets/3bee8723-a4df-4a66-9c06-7b3658e163a1" /><figcaption><i>Hình 1.6 Biểu đồ số lượt chơi của người chơi ở level hiện tại</i></figcaption></figure></div>


# Advertisement

## I. Bộ lọc

### 1. Ad Type

Loại quảng cáo

### 2. Ad Where

Vị trí quảng cáo được xem.&#x20;

## II. Biểu đồ

### 1. Tổng lượt xem quảng cáo theo Date(Imp by Date)

**Định nghĩa:** Biểu đồ thể hiện tổng số lượt xem quảng cáo của game qua các ngày.

**Ý nghĩa**: Kiểm tra tình hình biến động quảng cáo của game.

**Công thức**: &#x20;

* Từ các bản ghi AdLog, ta có:

$$
\begin{split}
Imp\_{DayX} &= count(AdLog\_{Day=X})
\\\\&= Số~~bản~~ghi~~ad~~Log\_{Day = X}
\end{split}
$$

* Trong đó:
  * Day: thời gian ghi nhận log/thời gian người chơi mở game lần đầu, tuy vào lựa chọn filter
  * **Impression**: Tổng số lượng quảng cáo trong ngày.

**VD**: Hình 1.1 cho thấy sự tăng trưởng trong số lượt xem quảng cáo qua các ngày gần đây.

<div align="center"><figure><img width="1431" height="1031" alt="Image" src="https://github.com/user-attachments/assets/4cb497c1-d3b0-4a48-b87c-9707a46f1109" /><figcaption><i>Hình 1.1. Biểu đồ Tổng lượt xem quảng cáo theo date</i></figcaption></figure></div>

### 2. Tổng lượt xem quảng cáo theo Level(Imp by Level)

**Định nghĩa:** Biểu đồ thể hiện tổng số lượt xem quảng cáo của game theo level.

**Ý nghĩa**: Level:

* Nếu là maxPassedLevel: mỗi cột quảng cáo cao hay thấp sẽ phản ánh tính chất của level sau; tại đây nếu các cột:
  * Cao: level sau quá khó, người chơi không vượt qua được nên cần xem nhiều quảng cáo; cần cân bằng với drop rate để tránh việc khai thác quá mức khiến người chơi bỏ game.
  * Thấp : level sau quá dễ, người chơi vượt qua quá nhanh, nên thử nâng độ khó của level sau này.
* Nếu là currentLevel(chỉ sử dụng nếu game cho chơi lại level): cho thấy hiệu quả các level ảnh hưởng người chơi xem quảng cáo&#x20;
  * Level có mức quảng cáo cao có thể do:
    * Level đó tồn tại vấn đề (Tài nguyên chưa cân đốí, bug,...)  nên người chơi lợi dụng level đó để lấy tài nguyên.
    * Level hợp thị hiếu người chơi, khiến họ chơi nhiều lần
    * Level hiệu quả, khiến người chơi phải xem quảng cáo nhiều để qua level.
  * Level có mức quảng cáo thấp có thể là do&#x20;
    * Quá dễ(đối chiếu với [Độ khó trên người Pass](https://github.com/nguyenkimluong98/pixiu-insight/edit/master/README.md#1-%C4%91%E1%BB%99-kh%C3%B3-tr%C3%AAn-ng%C6%B0%E1%BB%9Di-pass-%C4%91%E1%BB%99-kh%C3%B3-v%E1%BB%9Bi-ng%C6%B0%E1%BB%9Di-pass) bên Level), có thể tăng độ khó/độ dài level
    * Level thiết kế kém nên player không chơi lại

**Công thức**: &#x20;

* Từ các bản ghi AdLog, ta có:

$$
\begin{split}
Imp\_{LvX} &= count(AdLog\_{Level=X})
\\\\&= Số~~bản~~ghi~~ad~~Log\_{Level= X}
\end{split}
$$

* Trong đó:
  * **Impression**: Tổng số lượng quảng cáo.

**VD**: Hình 2.1 cho thấy level 8 có số lượng quảng cáo được xem nhiều nhất.

<div align="center"><figure><img width="1426" height="1120" alt="Image" src="https://github.com/user-attachments/assets/c040e67a-a6d2-420c-955d-24377d19cf13" /><figcaption><p><i>Hình 2.1. Biểu đồ Tổng lượt xem quảng cáo theo Level</i></p></figcaption></figure></div>

Thanh kéo thể hiện tỉ lệ quảng cáo trên một tập level chiếm bao nhiêu so với tổng lượng quảng cáo trên toàn bộ level (Tỉ lệ phân bổ quảng cáo theo level).

<div align="center"><figure><img src="https://3413088624-files.gitbook.io/~/files/v0/b/gitbook-x-prod.appspot.com/o/spaces%2FjD7QfQ9Dr736LmutDGT1%2Fuploads%2FwEtUurAxuh3iTMtwL5vr%2Fimage.png?alt=media&#x26;token=77e3919e-cf35-4720-874b-98c94c2bc525" alt=""><figcaption><p><i>Hình 2.2. Thanh kéo thể hiện tỉ lệ phân bổ quảng cáo theo level</i></p></figcaption></figure></div>

### 3. Imp/DAU

**Định nghĩa:**  thể hiện số lượng quảng cáo trung bình của một người chơi theo ngày

**Công thức**: &#x20;

* Từ các bản ghi AdLog, ta có:

$$
\begin{split}
Imp\_{DayX} &= count(AdLog\_{Day=X})
\\\\&= Số~~bản~~ghi~~ad~~Log\_{Day = X}
\end{split}
$$

* Từ các bản ghi RetentionLog SDK tự động thu thập(1 người chơi login 1 ngày chỉ có 1 bản ghi duy nhất):

$$
\begin{split}
DAU\_{DayX} &= count(RetentionLog\_{Day=X})
\\\\&= Số~~bản~~ghi~~Retention~~Log\_{Day = X}
\end{split}
$$

* Từ 2 dữ liệu trên:

$$
\begin{split}
ImpDAU\_{DayX} &= Imp\_{Day=X}/DAU\_{Day=X}
\\\\&= Số~~quảng~~cáo~~ngày~~X/Số~~người~~chơi~~ngày~~X
\end{split}
$$

* Trong đó:
  * Day: thời gian ghi nhận log/thời gian người chơi mở game lần đầu, tuy vào lựa chọn filter
  * **Impression**: Tổng số lượng quảng cáo trong ngày.
  * **DAU(Daily Engaged User)**: Số lượng người chơi vào game trong ngày

**VD**: Hình 3.1 cho thấy trong ngày 09/03/2026, có 2,209 người chơi mở game, trung bình một người chơi mở game lên sẽ xem 43.91 quảng cáo.

<div align="center"><figure><img width="1429" height="997" alt="Image" src="https://github.com/user-attachments/assets/54848575-0b49-4010-974a-1d163d09645e" /><figcaption><i>Hình 3.1. Biểu đồ Impression/DAU</i></figcaption></figure></div>

### 4. Impression/DEU

**Định nghĩa:**  thể hiện số lượng quảng cáo trung bình của một người chơi có **xem quảng cáo** theo ngày

**Công thức**: &#x20;

* Từ các bản ghi AdLog, ta có:

$$
\begin{split}
Imp\_{DayX} &= count(AdLog\_{Day=X})
\\\\&= Số~~bản~~ghi~~ad~~Log\_{Day = X}
\end{split}
$$

$$
\begin{split}
DEU\_{DayX} &= count(AdLog\_{Day=X}.Player)
\\\\&= Số~~người~~chơi~~có~~Ad\~Log\_{Day = X}
\end{split}
$$

* Từ 2 dữ liệu trên:

$$
\begin{split}
ImpDEU\_{DayX} &= Imp\_{Day=X}/DEU\_{Day=X}
\\\\&= Số~~quảng~~cáo~~ngày~~X/Số~~người~~chơi~~có~~Ad~~Log~~ngày\~X
\end{split}
$$

* Trong đó:
  * **Day**: thời gian ghi nhận log/thời gian người chơi mở game lần đầu, tuy vào lựa chọn filter
  * **Impression**: Tổng số lượng quảng cáo trong ngày.
  * **DEU(Daily Engaged User)**: Số lượng ***người chơi xem quảng cáo*** trong ngày.

VD: Hình 4.1 cho thấy trong ngày 09/03/2026, có 1,834 người chơi mở game và xem ít nhất 1 quảng cáo, trung bình sẽ mỗi người chơi đã xem 52.89 quảng cáo.

<div align="center"><figure><img width="1428" height="1004" alt="Image" src="https://github.com/user-attachments/assets/b6837496-52fe-4ef1-ad9f-006aca663726" /><figcaption><i>Hình 4.1. Biểu đồ Impression/DEU</i></figcaption></figure></div>

### 5. Impression/LEU (Level)

**Định nghĩa:**  thể hiện số lượng quảng cáo trung bình của một người chơi tại 1 level

**Công thức**: &#x20;

* Từ các bản ghi AdLog, ta có:

$$
\begin{split}
Imp\_{LvX} &= count(AdLog\_{Level=X})
\\\\&= Số~~bản~~ghi~~ad~~Log\_{Lv = X}
\end{split}
$$

$$
\begin{split}
LEU\_{LvX} &= count(AdLog\_{Lv=X}.Player)
\\\\&= Số~~người~~chơi~~có~~Ad\~Log\_{Lv = X}
\end{split}
$$

* Từ 2 dữ liệu trên:

$$
\begin{split}
ImpLEU\_{LvX} &= Imp\_{Lv=X}/LEU\_{Level=X}
\\\\&= Số~~quảng~~cáo~~Lv~~X/Số~~người~~chơi~~có~~Ad~~Log~~tại~~Lv~~X
\end{split}
$$

* Trong đó:
  * **Day**: thời gian ghi nhận log/thời gian người chơi mở game lần đầu, tuy vào lựa chọn filter
  * **Impression**: Tổng số lượng quảng cáo trong ngày.
  * **LEU(Level Engaged User)**: Số lượng ***người chơi xem quảng cáo*** trong ngày.

**VD**: Hình 5.1 cho thấy ở level 69, có 353 người chơi mở game, trung bình một người chơi mở game lên sẽ xem 9.34 quảng cáo.

<div align="center"><figure><img width="1432" height="1077" alt="Image" src="https://github.com/user-attachments/assets/57d3c15a-2c6f-42a7-83be-30d1cd734f39" /><figcaption><i>Hình 5.1. Biều đồ Impression/LAU</i></figcaption></figure></div>

### 6. Revenue/DAU (ARPDAU)

**Định nghĩa:**  Average Revenue Per Daily Active User - là doanh thu từ quảng cáo trung bình trên mỗi người dùng hoạt động hàng ngày.

**Công thức**: &#x20;

* Từ các bản ghi AdLog, ta có:

$$
\begin{split}
Revenue\_{DayX} &= sum(AdLog\_{Day=X}.Revenue)
\\\\&= Doanh~~thu~~quảng~~cáo~~ngày\~X
\end{split}
$$

* Từ các bản ghi RetentionLog SDK tự động thu thập(1 người chơi login 1 ngày chỉ có 1 bản ghi duy nhất):

$$
\begin{split}
DAU\_{DayX} &= count(RetentionLog\_{Day=X})
\\\\&= Số~~bản~~ghi~~Retention~~Log\_{Day = X}
\end{split}
$$

* Từ 2 dữ liệu trên:

$$
\begin{split}
ARPDAU\_{DayX} &= Revenue\_{Day=X}/DAU\_{Day=X}
\\\\&= Doanh~~thu~~quảng~~cáo~~ngày~~X/Số~~người~~chơi~~ngày\~X
\end{split}
$$

* Trong đó:
  * Day: thời gian ghi nhận log/thời gian người chơi mở game lần đầu, tuy vào lựa chọn filter
  * **Impression**: Tổng số lượng quảng cáo trong ngày.
  * **DAU(Daily Engaged User)**: Số lượng người chơi vào game trong ngày

**VD**: Hình 6.1 cho thấy trong ngày 09/03/2026, với doanh thu từ các lượt xem quảng cáo là 253,19 ($), có 2,209 người dùng hoạt động -> ARPDAU là 0.11 (¢).

<div align="center"><figure><img width="1428" height="998" alt="Image" src="https://github.com/user-attachments/assets/b5621f23-b4df-4632-9d6a-9d6a4eb4b72d" /><figcaption><i>Hình 6.1. Biểu đồ ARPDAU</i></figcaption></figure></div>

### 7. eCPM ( Effective cost per thousand impressions)

**Định nghĩa:**  Là doanh thu kiếm được cho 1000 lượt hiển thị quảng cáo trên ứng dụng

**Ý nghĩa:** sử dụng để đo lường hiệu suất chiến dịch và thúc đẩy giá trị tối đa từ không gian quảng cáo.

**Công thức**: &#x20;

* Từ các bản ghi AdLog, ta có:

$$
\begin{split}
Revenue\_{DayX} &= sum(AdLog\_{Day=X}.Revenue)
\\\\&= Doanh~~thu~~quảng~~cáo~~ngày\~X
\end{split}
$$

$$
\begin{split}
Imp\_{DayX} &= count(AdLog\_{Day=X})
\\\\&= Số~~bản~~ghi~~ad~~Log~~ngày~~X
\end{split}
$$

* Từ 2 dữ liệu trên:

$$
\begin{split}
eCPM\_{DayX} &= Revenue\_{Day=X}/Imp\_{Day=X}
\\\\&= Doanh~~thu~~quảng~~cáo~~ngày~~X/Số~~quảng~~cáo~~ngày\~X
\end{split}
$$

* Trong đó:
  * Day: thời gian ghi nhận log/thời gian người chơi mở game lần đầu, tuy vào lựa chọn filter

**VD**: Hình 7.1 cho thấy trong ngày 09/03/2026, với doanh thu là 253,19 ($), và số lần hiển thị quảng cáo là 96,999 lần, ta sẽ tính được eCPM là 2.61 ($).

<div align="center"><figure><img width="1426" height="997" alt="Image" src="https://github.com/user-attachments/assets/f848b764-6d37-437e-bc8b-e7650a046b3d" /><figcaption><i>Hình 7.1. Biểu đồ eCPM</i></figcaption></figure></div>

# InApp

## I. Bộ lọc

### 1. Where

* Mua sản phẩm ở chỗ nào trong game&#x20;

### 2. Product

* Sản phẩm mà người chơi mua (được nạp tại cái vị trí (Where) khác nhau).

## I. Biểu đồ

### 1. Tổng Inapp theo ngày

Biểu đồ thể hiện lượng Inapp người chơi đã mua phân bổ theo ngày, có thể theo dõi được giai đoạn nào người chơi nạp mạnh để phát triển game.

* Đơn vị: USD

VD: Hình 1.1 cho thấy ngày 09/03/2026, người chơi nạp Inapp nhiều nhất trong tháng, và mạnh nhất khi mua `battle.pass`

<div align="center"><figure><img width="1429" height="1030" alt="Image" src="https://github.com/user-attachments/assets/37612024-0d87-451c-829d-5ca6f0af9105" /><figcaption><i>Hình 1.1. Biểu đồ Tổng Inapp theo ngày</i></figcaption></figure></div>

### 2. ARPDAU theo ngày

* ARPDAU: Average Revenue Per Daily Active User.
* Biểu đồ thể hiện doanh thu trung bình Inapp trên mỗi người chơi hoạt động hàng ngày.
* Công thức: ARPDAU = Tổng doanh thu hàng ngày / Tổng người chơi hoạt động hàng ngày
* VD: Hình 2.1 cho thấy ở ngày 08/03/2026, có doanh thu inapp là 19,99 $, có 1,182 người dùng hoạt động và ARPDAU là 0.02 ¢

<div align="center"><figure><img width="1427" height="998" alt="Image" src="https://github.com/user-attachments/assets/d6a75c2f-b186-4f15-ae0e-2a2395e6f15d" /><figcaption><i>Hình 2.1. Biểu đồ ARPDAU theo ngày</i></figcaption></figure></div>

### 3. Tổng Inapp theo Level

Biểu đồ thể hiện lượng Inapp người chơi đã mua phân bổ theo level, có thể theo dõi được ở level nào người chơi nạp mạnh để phát triển game.

* Đơn vị: USD

VD: Hình 3.1 cho thấy ở level 3, người chơi nạp Inapp nhiều nhất tính trong khoảng level 3-111.

<div align="center"><figure><img width="1425" height="1117" alt="Image" src="https://github.com/user-attachments/assets/d0f52e9a-bcc0-4f10-919e-6a2deac9c1b4" /><figcaption><i>Hình 3.1. Biểu đồ Tổng Inapp theo Level</i></figcaption></figure></div>

Thanh kéo thể hiện tỉ lệ nạp Inapp trên một tập level chiếm bao nhiêu so với tổng lượng nạp Inapp trên toàn bộ level (Tỉ lệ phân bổ lượng Inapp theo level).

<div align="center"><figure><img src="https://3413088624-files.gitbook.io/~/files/v0/b/gitbook-x-prod.appspot.com/o/spaces%2FjD7QfQ9Dr736LmutDGT1%2Fuploads%2FaEDdmo8cM5m633kOXaSx%2Fimage.png?alt=media&#x26;token=f7f120c8-b960-418f-829c-633aa2cee343" alt=""><figcaption><p><i>Hình 3.2. Thanh kéo thể hiện tỉ lệ phân bổ lượng Inapp theo level</i></p></figcaption></figure></div>

### 4. ARPDAU theo Level

* Biểu đồ thể hiện doanh thu trung bình Inapp trên mỗi người chơi hoạt động theo từng level.
* Công thức: ARPDAU theo level = Tổng doanh thu mỗi level  / Tổng người chơi hoạt động mỗi level
* VD: Hình 4.1 cho thấy ở level 51, có doanh thu inapp là 7.99 $, có 223 người dùng hoạt động và ARPDAU là 0.04 ¢

<div align="center"><figure><img width="1432" height="1080" alt="Image" src="https://github.com/user-attachments/assets/556906ce-056f-4981-9e20-1539805954fd" /><figcaption><i>Hình 4.1 Biểu đồ ARPDAU theo level</i></figcaption></figure></div>

### 5. ARPPU theo ngày

* ARPPU: Average revenue per paying user- là phép đo được sử dụng để tính toán lượng doanh thu được tạo ra bởi người dùng, người chơi trả phí, trung binh, trong một khoảng thời gian nhất định.
* Biểu đồ biểu thị doanh thu trung bình trên mỗi người dụng nap inapp.
* Công thức tính: ARPPU = Tổng doanh thu tạo ra trong khoảng thời gian X / Tổng số người dùng nạp inapp trong khoảng thời gian X.
* Ví dụ: Theo hình 5.1 tại thời điển ngày 09/03/2026, có doanh thu là 24,96 ($) , số lượng người dùng nạp inapp là 2 -> ARPPU = 12.48 (¢).

<div align="center"><figure><img width="1428" height="1000" alt="Image" src="https://github.com/user-attachments/assets/8bb09e2e-5b9b-48c9-989a-78795ecabbde" /><figcaption><i>Hình 5.1. Biểu đồ ARPPU theo ngày</i></figcaption></figure></div>

### 6. Paying Rate theo ngày

* Biểu đồ biểu thị tỉ lệ người chơi nạp inapp so với tổng số người chơi hoạt động hàng ngày. Điều này cung cấp thông tin chi tiết về hiệu quả kiếm tiền và mức độ tương tác của trò chơi.
* Công thức: Paying Rate = Tổng số người dùng nạp inapp trong 1 khoảng thời gian X  \* 100 / Tổng số người dụng hoạt động trong khoảng thời gian X.
* Ví dụ: Hình 6.1 tại ngày 09/03/2026, có 2 người dùng nạp inapp, số lượng người dụng hoạt động là 1,235 -> Paying Rate sẽ là 0.16%.

<div align="center"><figure><img width="1429" height="999" alt="Image" src="https://github.com/user-attachments/assets/518d5bc0-023a-4bff-9053-7c8fbe2d5265" /><figcaption><i>Hình 6.1 Biểu dồ Paying Rate theo ngày</i></figcaption></figure></div>

# Funnel

Chưa có gì cả
