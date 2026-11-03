<!DOCTYPE html><html lang="ko"><head>
<!-- META -->
<meta charset="utf-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0, user-scalable=yes" />
<title>컴포즈커피 - A/B Test</title>

<!-- 컴포즈커피 기본 레이아웃 CSS 스타일 -->
<link rel="stylesheet" href="/layouts/composecoffee2025/css/general.css" />
<link rel="stylesheet" href="/layouts/composecoffee2025/css/sub.css" />
<link rel="stylesheet" href="/modules/cafemenu/skins/default/css/list_gallery.css" />
<link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css" />

<style>
  /* A/B 테스트 버전 구분을 위한 상단 안내 배너 */
  .ab-test-banner {
    background-color: #fed900;
    color: #000;
    text-align: center;
    padding: 12px;
    font-weight: bold;
    font-size: 15px;
    border-bottom: 2px solid #000;
  }
</style>
</head>

<body>
  <!-- 현재 접속 중인 A/B 테스트 버전을 표시하는 배너 -->
  <div id="ab-variant-indicator" class="ab-test-banner">
    A/B 테스트 버전을 불러오는 중입니다...
  </div>

  <header>
    <div class="hd_bt">
      <div class="hd_con">
        <h2 class="lg">
          <a href="/" title="컴포즈커피">
            <img src="/layouts/composecoffee2025/img/logo.svg" alt="LOGO" />
          </a>
        </h2>
      </div>
    </div>
  </header>

  <div class="wrapper">
    <article class="article" id="content">
      <div class="art_con">
        <div class="cafemenu-page" id="listPage">
          <div class="cafemenu-container">
            <div class="cafemenu-menu-header">
              <h1>MENU</h1>
              <p>컴포즈커피의 다양한 메뉴를 만나보세요 (A/B Test: 광고 모델 이미지 vs 음료 단독 이미지)</p>
            </div>

            <!-- Desktop Category Filter -->
            <div class="cafemenu-category-filter">
              <a href="#" class="cafemenu-category-btn active">전체</a>
              <a href="#" class="cafemenu-category-btn">추천메뉴</a>
              <a href="#" class="cafemenu-category-btn">커피ㆍ콜드브루</a>
              <a href="#" class="cafemenu-category-btn">베버리지</a>
              <a href="#" class="cafemenu-category-btn">티</a>
            </div>

            <!-- Variant에 따라 카페 메뉴가 동적으로 생성됩니다 -->
            <div class="cafemenu-menu-grid" id="product-grid">
              <!-- 메뉴 항목 삽입 위치 -->
            </div>

          </div>
        </div>
      </div>
    </article>
  </div>

<!-- A/B 테스트 로직 스크립트 -->
<script>
  // 1. 카페 메뉴 데이터 (A: 모델/사람이 든 음료 이미지, B: 음료 단독 이미지)
  const menuItems = [
    {
      id: "menu01",
      name: "I-청귤캐모마일티",
      imgModel: "https://images.unsplash.com/photo-1544717305-2782549b5136?w=500", // 사람이 음료/잔을 들고 있는 이미지
      imgProductOnly: "/files/attach/images/272857/037/348/03d229fc4c4404a6d6ab32d18f93330e.jpg" // 원본 누끼/단독 이미지
    },
    {
      id: "menu02",
      name: "I-딥라떼",
      imgModel: "https://images.unsplash.com/photo-1517256064527-09c73fc73e38?w=500", 
      imgProductOnly: "/files/attach/images/272857/844/343/15b3e049f37bfa7cbdbebf56e96efa9c.jpg"
    },
    {
      id: "menu03",
      name: "I-슈 클라우드 라떼",
      imgModel: "https://images.unsplash.com/photo-1495474472287-4d71bcdd2085?w=500",
      imgProductOnly: "/files/attach/images/272857/150/344/03befed4c129906d052fb999e27ce07d.jpg"
    },
    {
      id: "menu04",
      name: "PB&J 와플",
      imgModel: "https://images.unsplash.com/photo-1562376552-0d160a2f238d?w=500",
      imgProductOnly: "/files/attach/images/272857/164/344/fcfc469ce1ba6e7450ab3e7dae4d2e3e.jpg"
    }
  ];

  // 2. 사용자를 50:50 비율로 A 또는 B 그룹으로 나누는 함수
  function getABVariant() {
    let variant = localStorage.getItem('ab_test_variant');
    if (!variant) {
      variant = Math.random() < 0.5 ? 'A' : 'B';
      localStorage.setItem('ab_test_variant', variant);
    }
    return variant;
  }

  // 3. Variant에 맞춰 카페 메뉴 목록을 렌더링하는 함수
  function renderProducts() {
    const variant = getABVariant();
    const gridContainer = document.getElementById('product-grid');
    const indicator = document.getElementById('ab-variant-indicator');

    // 상단 안내 문구 업데이트
    if (variant === 'A') {
      indicator.innerHTML = "📌 <b>Variant A:</b> <b>광고 모델/사람 연출 이미지</b>가 표시됩니다.";
    } else {
      indicator.innerHTML = "📌 <b>Variant B:</b> <b>메뉴/음료 단독 이미지</b>가 표시됩니다.";
    }

    // 컴포즈커피 원본 레이아웃 구조 그대로 생성
    gridContainer.innerHTML = menuItems.map(item => {
      const imgSrc = (variant === 'A') ? item.imgModel : item.imgProductOnly;
      
      return `
        <a href="#" class="cafemenu-menu-item" data-menu-id="${item.id}" onclick="trackClick('${item.id}', '${variant}')">
          <div class="cafemenu-menu-image">
             <img src="${imgSrc}" alt="${item.name}">
          </div>
          <div class="cafemenu-menu-info">
             <div class="cafemenu-menu-name">${item.name}</div>
          </div>
        </a>
      `;
    }).join('');
  }

  // 4. 클릭 이벤트 트래킹
  function trackClick(menuId, variant) {
    console.log(`[A/B Test Analytics] 메뉴 클릭: ${menuId}, 그룹: ${variant}`);
    alert(`[A/B 테스트] ${variant} 그룹에서 ${menuId} 메뉴를 클릭했습니다!`);
  }

  // 페이지 로드 시 실행
  document.addEventListener("DOMContentLoaded", renderProducts);
</script>
</body>
</html>
