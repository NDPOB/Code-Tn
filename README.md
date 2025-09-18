# Code-Tn
Code Trắc nghiệm
function doGet(e) {
  // ====== THÔNG SỐ CẦN SỬA ======
  const FORM_ID = '1xr6n7AxtLPzMMLtv0rPETkT1K_nUEkpyUucDoGfiewc'; // Form cố định
  const SHEET_QUESTIONS_ID = '18n8GwdE3eQ1SmPtE_QlbGg8BTwHN_gF8puXlNCjYBsQ'; // Ngân hàng câu hỏi
  const SO_CAU = 10;
  // ==============================

  const form = FormApp.openById(FORM_ID);
  form.setIsQuiz(true);

  // Thêm ngày giờ vào tiêu đề
  const now = new Date();
  const timeZone = Session.getScriptTimeZone();
  const formattedDate = Utilities.formatDate(now, timeZone, "dd/MM/yyyy HH:mm");
  form.setTitle(`Đề kiểm tra ngẫu nhiên - ${formattedDate}`);
  
  // Xóa tất cả câu hỏi cũ
  form.getItems().forEach(item => form.deleteItem(item));

  // Lấy câu hỏi từ Sheet A
  const sheet = SpreadsheetApp.openById(SHEET_QUESTIONS_ID).getSheets()[0];
  const data = sheet.getDataRange().getValues();
  data.shift(); // bỏ hàng tiêu đề

  shuffleArray(data);
  const selected = data.slice(0, SO_CAU);

  // Tạo câu hỏi trong Form
  selected.forEach((row, index) => {
    const [question, optA, optB, optC, optD, answer] = row;
    const item = form.addMultipleChoiceItem();
    item.setTitle(`Câu hỏi ${index + 1}: ${question}`)
        .setChoices([
          item.createChoice(optA, answer === 'A'),
          item.createChoice(optB, answer === 'B'),
          item.createChoice(optC, answer === 'C'),
          item.createChoice(optD, answer === 'D'),
        ])
        .setPoints(10); // Mỗi câu đúng 10 điểm
  });

  // Lấy link để học sinh làm bài
  const formUrl = form.getPublishedUrl();

  // Chuyển hướng sang Form
  return HtmlService.createHtmlOutput(
    `<script>
       window.location.href = "${formUrl}";
     </script>`
  );
}

function shuffleArray(array) {
  for (let i = array.length - 1; i > 0; i--) {
    const j = Math.floor(Math.random() * (i + 1));
    [array[i], array[j]] = [array[j], array[i]];
  }
}
