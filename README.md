# 🚀 Edit Grid (Parent - Child)

**Webresource Edit Grid** hỗ trợ hiển thị và chỉnh sửa dữ liệu động theo cơ chế 2 tầng (Parent và Child) dựa trên cấu hình tùy biến (Configuration).

---

## ✨ Tính năng nổi bật

- 📝 **Edit Grid Parent:** Chỉnh sửa dữ liệu trực tiếp trên lưới cấp cha.
- 🌿 **Edit Grid Child:** Hỗ trợ mở rộng và chỉnh sửa lưới cấp con độc lập.
- ⚙️ Bộ hàm SDK mạnh mẽ:
  - Hỗ trợ đầy đủ các thao tác `setValue`, `setDisabled`, `setRequired`, `addOnChange`, `getValue`, `getValues`,`setFetchLookup`,`setBackgroudColor`,`show/hide button save` cho từng dòng.
  - Hỗ trợ đầy đủ các thao tác `show, hide` cho nút `new, delete, save`.
- 🎨 **Giao diện trực quan:** Tối ưu hóa trải nghiệm nhập liệu, dễ dàng thao tác và cấu hình.

---

## 🛠️ Yêu cầu cấu hình hệ thống

Để công cụ hoạt động chính xác, hãy hoàn thành đầy đủ các bước thiết lập dưới đây:

### Bước 1: Gắn Webresource vào Form
1. Tạo một **Webresource** trên Form của Dynamics 365 với webreource name: `ctt_/editGrid.html`.
2. Cấu hình **Custom Parameter (data)** cho Webresource này chính là mã định danh của Grid (Ví dụ: `acc_contact`).

### Bước 2: Thiết lập bảng `ctt_editgridconfiguration`
Tạo một bản ghi cấu hình trong bảng `ctt_editgridconfiguration` với thông tin chi tiết như sau:

* **Grid Code:** Phải trùng khớp hoàn toàn với mã *Custom Parameter* đã gắn ở Bước 1.
* **Label Grid:** Là tên hiển thị của grid.

#### 📊 Chi tiết thông số cấu hình cấu trúc Grid:

| Tên trường (Field) | Kiểu dữ liệu | Ví dụ thực tế | Mô tả chi tiết |
| :--- | :--- | :--- | :--- |
| **[Parent]** Entity Logical Name | Text | `contact` | Tên logic (Logical Name) của Entity cấp cha. |
| **[Parent]** View ID | GUID | `0000-0000-...` | ID của View dùng để hiển thị dữ liệu lưới cha. |
| **[Parent]** Parent Logical Name | Text | `accountid` | Trường Lookup kết nối từ lưới cha về Form hiện tại. |
| **[Parent]** List Field Edit | Text (dấu `,`) | `fullname,phone` | Danh sách các trường cho phép chỉnh sửa ở lưới cha. |
| **[Parent]** Has Child | Boolean | `True` | Bật tính năng nếu muốn cấu hình thêm lưới cấp con. |
| ─── | ─── | ─── | ─────────────────────────────────────────── |
| **[Child]** Entity Logical Name Child | Text | `contactdetail` | Tên logic (Logical Name) của Entity cấp con. |
| **[Child]** View ID Child | GUID | `0000-0000-...` | ID của View dùng để hiển thị dữ liệu lưới con. |
| **[Child]** Parent Logical Name Child | Text | `contactid` | Trường Lookup kết nối từ lưới con lên lưới cha. |
| **[Child]** List Field Edit Child | Text (dấu `,`) | `fullname,address` | Danh sách các trường cho phép chỉnh sửa ở lưới con. |

---


## 🚀 Hướng dẫn sử dụng & SDK API

Nếu không có logic đặc biệt, hệ thống sẽ tự động chạy sau khi cấu hình xong các bước trên. 

Trường hợp cần can thiệp logic động (Ràng buộc dữ liệu, Đóng/Mở khóa trường), bạn hãy sử dụng bộ công cụ **BiSDK** đi kèm:

### 📐 Định nghĩa các biến và đối tượng chính
- `gridName`: Mã cấu hình Grid (gắn ở tham số Webresource).
- `attribute`: Tên logic của trường dữ liệu cần xử lý.
- `Parent`: Thao tác trên Grid cấp cha.
- `Child`: Thao tác trên Grid cấp con.

### 🔌 Đăng ký sự kiện khởi tạo hệ thống (Onload)

Luôn đặt các logic tùy biến bên trong sự kiện `Loaded` để đảm bảo lưới dữ liệu đã được dựng thành công:

```javascript
// Kiểm tra trạng thái tải của webresource
BiSDK.Grid(gridName).Parent.getAttribute(attribute).Loaded((loaded: boolean) => {
    if (loaded) {
        // Webresource đã được tải, viết các hàm xử lý dữ liệu tại đây

        //Add OnChange => Callback result = { value: any, target: htmlElement };
        BiSDK.Grid(gridName).Parent.getAttribute(attribute).addOnChange((result) => {});

        //Get value => Return value
        BiSDK.Grid(gridName).Parent.getAttribute(attribute).getValue(target);

        //Get values => Callback result = { value: any, target: htmlElement };
        BiSDK.Grid(gridName).Parent.getAttribute(attribute).getValues((result) => {});

        //Set value theo định dạng value trả về => Return value
        BiSDK.Grid(gridName).Parent.getAttribute(attribute).setValue(value,target);

        //Set disabled
        BiSDK.Grid(gridName).Parent.getAttribute(attribute).setDisabled(bool,target);

        //Set required
        BiSDK.Grid(gridName).Parent.getAttribute(attribute).setRequired(bool,target);

        //Set fetchLookup
        var fetchXml = [
            "<fetch>",
            `  <entity name='entityname'>`,
            `    <attribute name='entitynameid'/>`,
            `    <attribute name='attribute'/>`,
            "    <filter>",
            "      <condition attribute='statecode' operator='eq' value='0'/>",
            "    </filter>",
            "  </entity>",
            "</fetch>"
        ].join("");
        BiSDK.Grid(gridName).Parent.getAttribute(attribute).setFetchLookup(fetchXml,target);

        //Show button new
        BiSDK.Grid(gridName).Button.Add.show();

        //Hide button new
        BiSDK.Grid(gridName).Button.Add.hide();

        //Show button save
        BiSDK.Grid(gridName).Button.Save.show();

        //Hide button save
        BiSDK.Grid(gridName).Button.Save.hide();

        //Show button delete
        BiSDK.Grid(gridName).Button.Delete.show();

        //Hide button delete
        BiSDK.Grid(gridName).Button.Delete.hide();

        //Show button save cho từng row
        BiSDK.Grid(gridName).Parent.Button.Save.show(rs.target);

        //Hide button save cho từng row
        BiSDK.Grid(gridName).Parent.Button.Save.hide(rs.target);

        //Set background color cho từng row
        BiSDK.Grid(gridName).Parent.UI.setBackgroudColor(rs.target);
    }
});
```

### 📋 Cấu trúc dữ liệu trả về (Data Structure)
Khi sử dụng các hàm lấy dữ liệu (`addOnChange`, `getValue`, `getValues`), hệ thống trả về một đối tượng chứa cấu trúc sau:

```javascript
results = { 
    value: any,           // Giá trị của trường
    target: htmlElement   // Đối tượng HTML của ô dữ liệu đang tương tác (bắt buộc truyền vào hàm set)
};

// Chi tiết định dạng của thuộc tính 'value' theo từng loại trường:
// 1. Trường OptionSet:  { value: number, label: string }
// 2. Trường Lookup:     { id: string, entityName: string, name: string }
// 3. Trường cơ bản:     string | number | boolean
```

---

## 💡 Ví dụ thực tế (Trường hợp nâng cao)

**Kịch bản:** Khi lưới `acc_contact` tải xong, chúng ta cần:
1. Đọc cột `statecode` để kiểm tra: Nếu giá trị bằng `1` thì show button `save`, ngược lại thì hide.
2. Lắng nghe sự kiện thay đổi (`addOnChange`) của cột `statecode`: Nếu giá trị khác `1` thì khóa cột `fullname`, ngược lại thì mở khóa.
3. Kiểm tra nếu user có role là admin thì mới hiển thị button delete, ngược lại thì hide.

```javascript
const grid = BiSDK.Grid("acc_contact");

grid.Loaded(async (loaded) => {
    if (!loaded) return;

    // 1. Kiểm tra điều kiện show/hide button save
    grid.Parent.getAttribute("statecode").getValues((rs) => {
        const isNew = rs.value?.value;
        if(isNew){
            grid.Button.Save.show(rs.target);
        }
        else{
            grid.Button.Save.hide(rs.target);
        }
    });

    // 2. Bắt sự kiện OnChange để thay đổi thuộc tính động khi người dùng thao tác
    grid.Parent.getAttribute("statecode").addOnChange((rs) => {
        const isStateActive = (rs?.value?.value === 1);
        grid.Parent.getAttribute("fullname").setDisabled(!isStateActive, rs.target);
    });

    // 3. Check role là admin show/hide button delete
    const roleAdmin = await getRoleAdmin();
    if(roleAdmin){
        grid.Button.Delete.show();
    }
    else{
        grid.Button.Delete.hide();
    }
});
```
