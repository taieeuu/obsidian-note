---
Date: 2024-08-17
---
`Generic Date Views` 是一組基於時間的通用視圖，用於顯示與日期和時間相關的對象。

根據模型裡的某個日期自段進行數據篩選，然後將符合結果的數據以一定的刑事顯示在網頁上，簡單來說，就是在列表視圖 `ListView` 或詳細視圖 `DetailView` 的基礎上增加日期篩選所實現的。

- **ArchiveIndexView**: 用於顯示對象的歸檔索引頁面，通常按日期排序。
- **YearArchiveView**: 用於顯示特定年份的對象列表。
- **MonthArchiveView**: 用於顯示特定月份的對象列表。
- **WeekArchiveView**: 用於顯示特定星期的對象列表。
- **DayArchiveView**: 用於顯示特定日期的對象列表。
- **TodayArchiveView**: 用於顯示當天的對象列表。
- **DateDetailView**: 用於顯示特定日期和時間的單個對象的詳細信息。