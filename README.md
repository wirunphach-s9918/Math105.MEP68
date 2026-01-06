<html lang="th" class="h-full">
 <head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>ระบบประกาศคะแนนสอบ</title>
  <script src="/_sdk/element_sdk.js"></script>
  <script src="https://cdn.tailwindcss.com"></script>
  <link rel="preconnect" href="https://fonts.googleapis.com">
  <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
  <link href="https://fonts.googleapis.com/css2?family=Mitr:wght@300;400;500;600;700&amp;display=swap" rel="stylesheet">
  <style>
    body {
      box-sizing: border-box;
      font-family: 'Mitr', sans-serif;
    }
  </style>
  <style>@view-transition { navigation: auto; }</style>
  <script src="/_sdk/data_sdk.js" type="text/javascript"></script>
 </head>
 <body class="h-full">
  <div id="app" class="h-full w-full"></div>
  <script>
    const PASSWORD = "105MEP68";
    const MAX_SCORE = 20;
    
    const defaultConfig = {
      school_name: "โรงเรียนประตูชัย",
      class_name: "ชั้นประถมศึกษาปีที่ 1/5",
      subject_name: "สาย MEP (Mini English Program)",
      teacher_name: "นางวิรัลพัชษ์ สว่างเดือน",
      background_color: "#fdf4ff",
      card_color: "#ffffff",
      primary_color: "#a855f7",
      text_color: "#581c87",
      accent_color: "#ec4899",
      font_family: "Mitr",
      font_size: 16
    };

    // ข้อมูลนักเรียนทั้งหมด
    const studentsData = {
      "22257": { name: "เด็กชายธราธร สังสีแก้ว", lesson1: 7, lesson2: 7 },
      "22262": { name: "เด็กชายนิรภัฏ สัมมาชีพ", lesson1: null, lesson2: 12.5 },
      "22293": { name: "เด็กชายชย พึ่งเคหา", lesson1: 12, lesson2: 5 },
      "22296": { name: "เด็กชายนฤวัต สัมมาชีพ", lesson1: null, lesson2: 10 },
      "22775": { name: "เด็กชายวรากร มีลือกิจ", lesson1: 9, lesson2: 3 },
      "23337": { name: "เด็กชายพัฒนพล พัฒนตั้งสกุล", lesson1: 17, lesson2: 19 },
      "23779": { name: "เด็กชายชยพล สีสด", lesson1: 10, lesson2: 6 },
      "23780": { name: "เด็กชายรณพีร์ มะนะมุติ", lesson1: 16, lesson2: 18 },
      "23781": { name: "เด็กชายธีช์ศิริวุตม์ ชูชื่น", lesson1: 10, lesson2: 4 },
      "23782": { name: "เด็กชายกานต์รวี รักซ้อน", lesson1: 14, lesson2: 14 },
      "22276": { name: "เด็กหญิงศรัญญา ขยันกิจ", lesson1: 14, lesson2: 3 },
      "22305": { name: "เด็กหญิงกันต์กนิษฐ์ กุฎีพันธ์", lesson1: 13, lesson2: 7 },
      "22314": { name: "เด็กหญิงกมลพรรณ บัวสาย", lesson1: 15, lesson2: 12.5 },
      "22783": { name: "เด็กหญิงจารวี สุนทรศารทูล", lesson1: 11, lesson2: 9 },
      "22790": { name: "เด็กหญิงสุณัฎฐา ตีเมืองสอง", lesson1: 13, lesson2: null },
      "23783": { name: "เด็กหญิงสมิตา ยิ้มทอง", lesson1: 9, lesson2: null },
      "23784": { name: "เด็กหญิงกีรติญา วิทูรัตน์", lesson1: 9, lesson2: 4 },
      "23785": { name: "เด็กหญิงสุชาพร สว่างสดิพิศาล", lesson1: 7, lesson2: 1 },
      "23786": { name: "เด็กหญิงพรพิมล สุวรรณเกตุ", lesson1: 14, lesson2: 14 },
      "23787": { name: "เด็กหญิงพัชรณัฏฐ์ อยู่เพ็ชร", lesson1: 14, lesson2: 9 },
      "23788": { name: "เด็กหญิงกมลวรรณ ขยายฤทธิ์", lesson1: 6, lesson2: 3 },
      "23789": { name: "เด็กหญิงปวิชญา ทองสมบัติ", lesson1: 10, lesson2: 6 },
      "23790": { name: "เด็กหญิงปภาวรินทร์ บุญเพ็ง", lesson1: 11, lesson2: 10 },
      "23791": { name: "เด็กหญิงคคนานต์ ชัยทร", lesson1: 9, lesson2: 2 },
      "23792": { name: "เด็กหญิงณัฐกฤตา สังวียนทอง", lesson1: 11, lesson2: 7 },
      "23793": { name: "เด็กหญิงปพิชญา เตชะสถิตย์กุล", lesson1: 14, lesson2: 12 },
      "23933": { name: "เด็กหญิงพิชญธิดา สุขสมโภชน์", lesson1: 10, lesson2: 5 },
      "23934": { name: "เด็กหญิงน้ำทิพย์ วัชรเวียงชัย", lesson1: 10, lesson2: 7 },
      "23935": { name: "เด็กหญิงนิฤมล ใจเยือกเย็น", lesson1: 8, lesson2: 1 },
      "23936": { name: "เด็กหญิงธัญชนก เกตุจุฬา", lesson1: 10, lesson2: 3 }
    };

    let currentStudentId = null;

    async function initApp() {
      if (window.elementSdk) {
        window.elementSdk.init({
          defaultConfig,
          onConfigChange: async (config) => {
            applyConfig(config);
            if (currentStudentId) {
              renderScoresPage();
            } else {
              renderLoginPage();
            }
          },
          mapToCapabilities: (config) => ({
            recolorables: [
              {
                get: () => config.background_color || defaultConfig.background_color,
                set: (value) => {
                  config.background_color = value;
                  window.elementSdk.setConfig({ background_color: value });
                }
              },
              {
                get: () => config.card_color || defaultConfig.card_color,
                set: (value) => {
                  config.card_color = value;
                  window.elementSdk.setConfig({ card_color: value });
                }
              },
              {
                get: () => config.text_color || defaultConfig.text_color,
                set: (value) => {
                  config.text_color = value;
                  window.elementSdk.setConfig({ text_color: value });
                }
              },
              {
                get: () => config.primary_color || defaultConfig.primary_color,
                set: (value) => {
                  config.primary_color = value;
                  window.elementSdk.setConfig({ primary_color: value });
                }
              },
              {
                get: () => config.accent_color || defaultConfig.accent_color,
                set: (value) => {
                  config.accent_color = value;
                  window.elementSdk.setConfig({ accent_color: value });
                }
              }
            ],
            borderables: [],
            fontEditable: {
              get: () => config.font_family || defaultConfig.font_family,
              set: (value) => {
                config.font_family = value;
                window.elementSdk.setConfig({ font_family: value });
              }
            },
            fontSizeable: {
              get: () => config.font_size || defaultConfig.font_size,
              set: (value) => {
                config.font_size = value;
                window.elementSdk.setConfig({ font_size: value });
              }
            }
          }),
          mapToEditPanelValues: (config) => new Map([
            ["school_name", config.school_name || defaultConfig.school_name],
            ["class_name", config.class_name || defaultConfig.class_name],
            ["subject_name", config.subject_name || defaultConfig.subject_name],
            ["teacher_name", config.teacher_name || defaultConfig.teacher_name]
          ])
        });
      }

      renderLoginPage();
    }

    function applyConfig(config) {
      const app = document.getElementById('app');
      const customFont = config.font_family || defaultConfig.font_family;
      const baseSize = config.font_size || defaultConfig.font_size;
      const bgColor = config.background_color || defaultConfig.background_color;
      
      app.style.background = bgColor;
      app.style.fontFamily = `${customFont}, sans-serif`;
      app.style.fontSize = `${baseSize}px`;
      
      document.documentElement.style.setProperty('--card-color', config.card_color || defaultConfig.card_color);
      document.documentElement.style.setProperty('--text-color', config.text_color || defaultConfig.text_color);
      document.documentElement.style.setProperty('--primary-color', config.primary_color || defaultConfig.primary_color);
      document.documentElement.style.setProperty('--accent-color', config.accent_color || defaultConfig.accent_color);
    }

    function renderLoginPage() {
      const config = window.elementSdk?.config || defaultConfig;
      const app = document.getElementById('app');
      const customFont = config.font_family || defaultConfig.font_family;
      const baseSize = config.font_size || defaultConfig.font_size;
      const cardColor = config.card_color || defaultConfig.card_color;
      const textColor = config.text_color || defaultConfig.text_color;
      const primaryColor = config.primary_color || defaultConfig.primary_color;
      const schoolName = config.school_name || defaultConfig.school_name;
      const className = config.class_name || defaultConfig.class_name;
      const subjectName = config.subject_name || defaultConfig.subject_name;
      const teacherName = config.teacher_name || defaultConfig.teacher_name;

      app.innerHTML = `
        <div class="h-full w-full flex items-center justify-center p-6" style="background: linear-gradient(135deg, #fdf4ff 0%, #fce7f3 50%, #dbeafe 100%); overflow-y: auto;">
          <div class="w-full max-w-md" style="background: ${cardColor}; border-radius: 24px; box-shadow: 0 12px 40px rgba(168, 85, 247, 0.25); padding: 48px; border: 3px solid rgba(236, 72, 153, 0.2);">
            <div style="text-align: center; margin-bottom: 32px;">
              <div style="font-size: ${baseSize * 1.76}px; font-weight: 700; background: linear-gradient(135deg, #a855f7 0%, #ec4899 50%, #60a5fa 100%); -webkit-background-clip: text; -webkit-text-fill-color: transparent; background-clip: text; margin-bottom: 12px; font-family: ${customFont}, sans-serif; line-height: 1.3;">
                🌟 ระบบประกาศคะแนนสอบ
              </div>
              <div style="font-size: ${baseSize * 1.76}px; font-weight: 700; background: linear-gradient(135deg, #a855f7 0%, #ec4899 50%, #60a5fa 100%); -webkit-background-clip: text; -webkit-text-fill-color: transparent; background-clip: text; margin-bottom: 16px; font-family: ${customFont}, sans-serif; line-height: 1.3;">
                วิชา คณิตศาสตร์
              </div>
              <div style="font-size: ${baseSize * 1.1}px; font-weight: 600; color: ${textColor}; margin-bottom: 8px; font-family: ${customFont}, sans-serif;">
                ${schoolName}
              </div>
              <div style="font-size: ${baseSize * 0.95}px; color: ${textColor}; opacity: 0.8; margin-bottom: 6px; font-family: ${customFont}, sans-serif;">
                ${className}
              </div>
              <div style="font-size: ${baseSize * 0.9}px; color: ${textColor}; opacity: 0.7; margin-bottom: 8px; font-family: ${customFont}, sans-serif;">
                ${subjectName}
              </div>
              <div style="font-size: ${baseSize * 0.95}px; font-weight: 700; color: ${textColor}; opacity: 0.85; font-family: ${customFont}, sans-serif;">
                ครูผู้สอน ${teacherName}
              </div>
            </div>

            <div style="margin-bottom: 24px; padding: 20px; background: linear-gradient(135deg, #eff6ff 0%, #f0f9ff 100%); border-radius: 16px; border: 2px solid #bfdbfe;">
              <div style="font-size: ${baseSize * 1.05}px; font-weight: 700; color: #1e40af; margin-bottom: 12px; font-family: ${customFont}, sans-serif; display: flex; align-items: center; gap: 8px;">
                <span style="font-size: ${baseSize * 1.3}px;">ℹ️</span>
                คำแนะนำการเข้าใช้งาน
              </div>
              <div style="font-size: ${baseSize * 0.85}px; color: #1e3a8a; line-height: 1.8; font-family: ${customFont}, sans-serif;">
                <div style="margin-bottom: 8px;">
                  <span style="font-weight: 600;">📌 เลขประจำตัวนักเรียน:</span> กรอก 5 หลัก เช่น 99999
                </div>
                <div style="margin-bottom: 8px;">
                  <span style="font-weight: 600;">🔐 รหัสผ่าน:</span> 105MEP68
                </div>
                <div style="margin-top: 12px; padding: 10px; background: white; border-radius: 8px; border-left: 3px solid #3b82f6;">
                  <span style="font-weight: 600;">💡 เคล็ดลับ:</span> หากเข้าสู่ระบบไม่ได้ ให้ตรวจสอบว่ากรอกเลขประจำตัว 5 หลักถูกต้อง และใช้รหัสผ่านที่ครูแจ้ง
                </div>
              </div>
            </div>

            <form id="loginForm" style="display: flex; flex-direction: column; gap: 20px;">
              <div>
                <label for="studentId" style="display: block; font-size: ${baseSize * 0.9}px; font-weight: 600; color: ${textColor}; margin-bottom: 8px; font-family: ${customFont}, sans-serif;">
                  เลขประจำตัวนักเรียน (5 หลัก)
                </label>
                <input 
                  type="text" 
                  id="studentId" 
                  maxlength="5"
                  pattern="[0-9]{5}"
                  required
                  style="width: 100%; padding: 14px 18px; border: 2px solid #e9d5ff; border-radius: 12px; font-size: ${baseSize}px; color: ${textColor}; font-family: ${customFont}, sans-serif; box-sizing: border-box; transition: all 0.3s;"
                  placeholder="xxxxx"
                  onfocus="this.style.borderColor='${primaryColor}'; this.style.boxShadow='0 0 0 3px rgba(168, 85, 247, 0.1)'"
                  onblur="this.style.borderColor='#e9d5ff'; this.style.boxShadow='none'"
                >
              </div>

              <div>
                <label for="password" style="display: block; font-size: ${baseSize * 0.9}px; font-weight: 600; color: ${textColor}; margin-bottom: 8px; font-family: ${customFont}, sans-serif;">
                  รหัสผ่าน
                </label>
                <input 
                  type="password" 
                  id="password" 
                  required
                  style="width: 100%; padding: 14px 18px; border: 2px solid #e9d5ff; border-radius: 12px; font-size: ${baseSize}px; color: ${textColor}; font-family: ${customFont}, sans-serif; box-sizing: border-box; transition: all 0.3s;"
                  placeholder="กรอกรหัสผ่าน"
                  onfocus="this.style.borderColor='${primaryColor}'; this.style.boxShadow='0 0 0 3px rgba(168, 85, 247, 0.1)'"
                  onblur="this.style.borderColor='#e9d5ff'; this.style.boxShadow='none'"
                >
              </div>

              <div id="errorMessage" style="display: none; padding: 14px; background: linear-gradient(135deg, #fee2e2 0%, #fce7f3 100%); border-radius: 12px; color: #dc2626; font-size: ${baseSize * 0.9}px; font-family: ${customFont}, sans-serif; border: 2px solid #fca5a5;"></div>

              <button 
                type="submit"
                style="width: 100%; padding: 16px; background: linear-gradient(135deg, ${primaryColor} 0%, #ec4899 100%); color: white; border: none; border-radius: 12px; font-size: ${baseSize * 1.1}px; font-weight: 600; cursor: pointer; transition: all 0.3s; font-family: ${customFont}, sans-serif; box-shadow: 0 4px 16px rgba(168, 85, 247, 0.3);"
                onmouseover="this.style.transform='translateY(-2px)'; this.style.boxShadow='0 8px 24px rgba(168, 85, 247, 0.4)'"
                onmouseout="this.style.transform='translateY(0)'; this.style.boxShadow='0 4px 16px rgba(168, 85, 247, 0.3)'"
              >
                ✨ เข้าสู่ระบบ
              </button>
            </form>
          </div>
        </div>
      `;

      document.getElementById('loginForm').addEventListener('submit', handleLogin);
    }

    function handleLogin(e) {
      e.preventDefault();
      const studentId = document.getElementById('studentId').value;
      const password = document.getElementById('password').value;
      const errorDiv = document.getElementById('errorMessage');

      if (studentId.length !== 5 || !/^\d{5}$/.test(studentId)) {
        errorDiv.textContent = "❌ กรุณากรอกเลขประจำตัวนักเรียน 5 หลักให้ถูกต้อง";
        errorDiv.style.display = 'block';
        return;
      }

      if (password !== PASSWORD) {
        errorDiv.textContent = "❌ รหัสผ่านไม่ถูกต้อง";
        errorDiv.style.display = 'block';
        return;
      }

      if (!studentsData[studentId]) {
        errorDiv.textContent = "❌ ไม่พบข้อมูลนักเรียนในระบบ";
        errorDiv.style.display = 'block';
        return;
      }

      currentStudentId = studentId;
      renderScoresPage();
    }

    function renderScoresPage() {
      const config = window.elementSdk?.config || defaultConfig;
      const app = document.getElementById('app');
      const customFont = config.font_family || defaultConfig.font_family;
      const baseSize = config.font_size || defaultConfig.font_size;
      const cardColor = config.card_color || defaultConfig.card_color;
      const textColor = config.text_color || defaultConfig.text_color;
      const primaryColor = config.primary_color || defaultConfig.primary_color;
      const accentColor = config.accent_color || defaultConfig.accent_color;
      const schoolName = config.school_name || defaultConfig.school_name;
      const className = config.class_name || defaultConfig.class_name;
      const subjectName = config.subject_name || defaultConfig.subject_name;
      const teacherName = config.teacher_name || defaultConfig.teacher_name;

      const studentData = studentsData[currentStudentId];
      const lessons = [
        { number: 1, name: "บทที่ 11: การวัดความยาว", icon: "📏", score: studentData.lesson1 },
        { number: 2, name: "บทที่ 12: การบวกที่ผลลัพธ์ไม่เกิน 100", icon: "➕", score: studentData.lesson2 }
      ];

      app.innerHTML = `
        <div class="h-full w-full" style="background: linear-gradient(135deg, #fdf4ff 0%, #fce7f3 50%, #dbeafe 100%); overflow-y: auto;">
          <div style="max-width: 800px; margin: 0 auto; padding: 32px 24px;">
            <div style="background: ${cardColor}; border-radius: 24px; padding: 36px; box-shadow: 0 8px 32px rgba(168, 85, 247, 0.2); margin-bottom: 28px; border: 3px solid rgba(236, 72, 153, 0.15);">
              <div style="display: flex; justify-content: space-between; align-items: center; flex-wrap: wrap; gap: 16px; margin-bottom: 20px;">
                <div>
                  <div style="font-size: ${baseSize * 1.9}px; font-weight: 700; background: linear-gradient(135deg, #a855f7 0%, #ec4899 50%, #60a5fa 100%); -webkit-background-clip: text; -webkit-text-fill-color: transparent; background-clip: text; margin-bottom: 10px; font-family: ${customFont}, sans-serif;">
                    💖 คะแนนสอบวิชาคณิตศาสตร์
                  </div>
                  <div style="font-size: ${baseSize * 1.05}px; font-weight: 600; color: ${textColor}; margin-bottom: 5px; font-family: ${customFont}, sans-serif;">
                    ${schoolName}
                  </div>
                  <div style="font-size: ${baseSize * 0.95}px; color: ${textColor}; opacity: 0.75; margin-bottom: 3px; font-family: ${customFont}, sans-serif;">
                    ${className}
                  </div>
                  <div style="font-size: ${baseSize * 0.9}px; color: ${textColor}; opacity: 0.7; margin-bottom: 5px; font-family: ${customFont}, sans-serif;">
                    ${subjectName}
                  </div>
                  <div style="font-size: ${baseSize * 0.95}px; font-weight: 700; color: ${textColor}; opacity: 0.85; font-family: ${customFont}, sans-serif;">
                    ครูผู้สอน ${teacherName}
                  </div>
                </div>
                <button 
                  id="logoutBtn"
                  style="padding: 12px 24px; background: white; color: ${primaryColor}; border: 2px solid ${primaryColor}; border-radius: 12px; font-size: ${baseSize * 0.9}px; font-weight: 600; cursor: pointer; font-family: ${customFont}, sans-serif; transition: all 0.3s;"
                  onmouseover="this.style.background='linear-gradient(135deg, ${primaryColor} 0%, ${accentColor} 100%)'; this.style.color='white'; this.style.transform='scale(1.05)'"
                  onmouseout="this.style.background='white'; this.style.color='${primaryColor}'; this.style.transform='scale(1)'"
                >
                  👋 ออกจากระบบ
                </button>
              </div>
              <div style="padding: 20px; background: linear-gradient(135deg, ${primaryColor} 0%, ${accentColor} 50%, #60a5fa 100%); border-radius: 16px; box-shadow: 0 4px 16px rgba(168, 85, 247, 0.3); margin-bottom: 16px;">
                <div style="font-size: ${baseSize * 0.9}px; color: white; opacity: 0.95; margin-bottom: 6px; font-family: ${customFont}, sans-serif;">
                  ⭐ เลขประจำตัวนักเรียน
                </div>
                <div style="font-size: ${baseSize * 1.6}px; font-weight: 700; color: white; font-family: ${customFont}, sans-serif;">
                  ${currentStudentId}
                </div>
              </div>
              <div style="padding: 18px; background: linear-gradient(135deg, #fae8ff 0%, #e0e7ff 100%); border-radius: 16px; border: 2px solid rgba(168, 85, 247, 0.2);">
                <div style="font-size: ${baseSize * 1.25}px; font-weight: 600; color: ${textColor}; font-family: ${customFont}, sans-serif;">
                  👤 ${studentData.name}
                </div>
              </div>
            </div>

            <div style="display: grid; grid-template-columns: repeat(auto-fit, minmax(300px, 1fr)); gap: 24px;">
              ${lessons.map(lesson => {
                const score = lesson.score;
                const percentage = score !== null ? ((score / MAX_SCORE) * 100).toFixed(2) : null;
                
                return `
                  <div 
                    style="background: ${cardColor}; border-radius: 20px; padding: 28px; box-shadow: 0 6px 24px rgba(168, 85, 247, 0.15); transition: all 0.3s; border: 3px solid ${score !== null ? 'rgba(168, 85, 247, 0.3)' : 'rgba(220, 38, 38, 0.3)'};"
                  >
                    <div style="font-size: ${baseSize * 1.25}px; font-weight: 700; color: ${textColor}; margin-bottom: 20px; font-family: ${customFont}, sans-serif; display: flex; align-items: center; gap: 8px;">
                      <span style="font-size: ${baseSize * 1.5}px;">${lesson.icon}</span>
                      ${lesson.name}
                    </div>
                    ${score !== null ? `
                      <div style="display: flex; align-items: baseline; gap: 10px; margin-bottom: 12px;">
                        <div style="font-size: ${baseSize * 2.8}px; font-weight: 700; background: linear-gradient(135deg, ${accentColor} 0%, ${primaryColor} 100%); -webkit-background-clip: text; -webkit-text-fill-color: transparent; background-clip: text; font-family: ${customFont}, sans-serif;">
                          ${score}
                        </div>
                        <div style="font-size: ${baseSize * 1.3}px; color: ${textColor}; opacity: 0.6; font-family: ${customFont}, sans-serif;">
                          / ${MAX_SCORE} คะแนน
                        </div>
                      </div>
                      <div style="padding: 12px 16px; background: linear-gradient(135deg, #fae8ff 0%, #e0e7ff 100%); border-radius: 12px; margin-bottom: 16px; border: 2px solid rgba(168, 85, 247, 0.2);">
                        <div style="font-size: ${baseSize * 1.8}px; font-weight: 700; color: ${primaryColor}; font-family: ${customFont}, sans-serif; text-align: center;">
                          ${percentage}%
                        </div>
                      </div>
                      <div style="width: 100%; height: 12px; background: linear-gradient(90deg, #fae8ff 0%, #ddd6fe 100%); border-radius: 8px; overflow: hidden; box-shadow: inset 0 2px 4px rgba(0,0,0,0.1); margin-bottom: 16px;">
                        <div style="height: 100%; background: linear-gradient(90deg, ${accentColor} 0%, ${primaryColor} 100%); width: ${percentage}%; transition: width 0.5s; box-shadow: 0 2px 8px rgba(236, 72, 153, 0.4);"></div>
                      </div>
                      ${(() => {
                        const percent = parseFloat(percentage);
                        let emoji = '';
                        let title = '';
                        let message = '';
                        
                        if (percent >= 70) {
                          emoji = '🌟';
                          title = 'ยอดเยี่ยมมาก!';
                          message = 'ผลงานของหนูแสดงให้เห็นถึงความตั้งใจและความเข้าใจที่ดีมาก ขอให้รักษามาตรฐานแบบนี้ไว้ และพัฒนาศักยภาพของตนเองต่อไป ครูเชื่อมั่นในความสามารถของหนูนะ';
                        } else if (percent >= 50) {
                          emoji = '💪';
                          title = 'ทำได้ดีขึ้นมากแล้ว';
                          message = 'ความพยายามของหนูเริ่มเห็นผลชัดเจนแล้ว ลองตั้งใจอีกนิด ฝึกฝนอย่างสม่ำเสมอ หนูจะก้าวไปถึงระดับที่สูงขึ้นได้แน่นอน';
                        } else if (percent >= 35) {
                          emoji = '✨';
                          title = 'หนูกำลังอยู่ระหว่างการพัฒนา';
                          message = 'อย่าท้อใจนะ ความก้าวหน้าเริ่มต้นจากการลงมือทำ ลองฝึกโจทย์ให้มากขึ้น ทบทวนสิ่งที่เรียนอย่างสม่ำเสมอ ครูพร้อมช่วยหนูเสมอ';
                        } else {
                          emoji = '🌱';
                          title = 'ทุกคนเรียนรู้ได้ในจังหวะของตนเอง';
                          message = 'ขอให้หนูเริ่มต้นจากการตั้งใจเรียน ทบทวนทีละนิด และฝึกอย่างต่อเนื่อง ความสำเร็จจะค่อย ๆ เกิดขึ้น ครูเชื่อว่าหนูทำได้';
                        }
                        
                        return `
                          <div style="padding: 16px; background: linear-gradient(135deg, #fefce8 0%, #fef3c7 100%); border-radius: 12px; border: 2px solid rgba(234, 179, 8, 0.3);">
                            <div style="font-size: ${baseSize * 1.1}px; font-weight: 700; color: #854d0e; margin-bottom: 8px; font-family: ${customFont}, sans-serif; display: flex; align-items: center; gap: 6px;">
                              <span style="font-size: ${baseSize * 1.3}px;">${emoji}</span>
                              ${title}
                            </div>
                            <div style="font-size: ${baseSize * 0.85}px; color: #713f12; line-height: 1.6; font-family: ${customFont}, sans-serif;">
                              ${message}
                            </div>
                          </div>
                        `;
                      })()}
                    ` : `
                      <div style="padding: 32px 0; text-align: center;">
                        <div style="font-size: ${baseSize * 2.5}px; margin-bottom: 12px;">😢</div>
                        <div style="font-size: ${baseSize * 1.3}px; font-weight: 700; color: #dc2626; font-family: ${customFont}, sans-serif;">
                          ขาดสอบ
                        </div>
                      </div>
                    `}
                  </div>
                `;
              }).join('')}
            </div>
          </div>
        </div>
      `;

      document.getElementById('logoutBtn').addEventListener('click', () => {
        currentStudentId = null;
        renderLoginPage();
      });
    }

    initApp();
  </script>
 <script>(function(){function c(){var b=a.contentDocument||a.contentWindow.document;if(b){var d=b.createElement('script');d.innerHTML="window.__CF$cv$params={r:'9b9b6cc613fd732e',t:'MTc2NzcwNDcwNS4wMDAwMDA='};var a=document.createElement('script');a.nonce='';a.src='/cdn-cgi/challenge-platform/scripts/jsd/main.js';document.getElementsByTagName('head')[0].appendChild(a);";b.getElementsByTagName('head')[0].appendChild(d)}}if(document.body){var a=document.createElement('iframe');a.height=1;a.width=1;a.style.position='absolute';a.style.top=0;a.style.left=0;a.style.border='none';a.style.visibility='hidden';document.body.appendChild(a);if('loading'!==document.readyState)c();else if(window.addEventListener)document.addEventListener('DOMContentLoaded',c);else{var e=document.onreadystatechange||function(){};document.onreadystatechange=function(b){e(b);'loading'!==document.readyState&&(document.onreadystatechange=e,c())}}}})();</script></body>
</html>
