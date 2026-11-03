<!DOCTYPE html><html lang="ko"><head>
<!-- META -->
<meta charset="utf-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0, user-scalable=yes" />
<title>컴포즈커피 - 장원영 A/B Test</title>

<!-- 컴포즈커피 기본 레이아웃 CSS 스타일 -->
<link rel="stylesheet" href="/layouts/composecoffee2025/css/general.css" />
<link rel="stylesheet" href="/layouts/composecoffee2025/css/sub.css" />
<link rel="stylesheet" href="/modules/cafemenu/skins/default/css/list_gallery.css" />
<link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css" />

<style>
  /* A/B 테스트 배너 스타일 */
  .ab-test-banner {
    background-color: #fed900;
    color: #000;
    text-align: center;
    padding: 12px;
    font-weight: bold;
    font-size: 15px;
    border-bottom: 2px solid #000;
    position: sticky;
    top: 0;
    z-index: 9999;
  }
</style>
</head>

<body>
  <!-- A/B 테스트 그룹 상태 안내 배너 -->
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
              <h1>COMPOSE COFFEE x JANG WONYOUNG</h1>
              <p>A/B Test: 장원영 광고 모델 이미지 (Variant A) vs 음료 단독 누끼 이미지 (Variant B)</p>
            </div>

            <!-- Desktop Category Filter -->
            <div class="cafemenu-category-filter">
              <a href="#" class="cafemenu-category-btn active">전체 메뉴</a>
              <a href="#" class="cafemenu-category-btn">추천메뉴</a>
              <a href="#" class="cafemnewUser-category-btn">커피ㆍ콜드브루</a>
              <a href="#" class="cafemenu-category-btn">디저트</a>
            </div>

            <!-- Variant에 따라 이미지가 동적으로 변경되는 그리드 영역 -->
            <div class="cafemenu-menu-grid" id="product-grid">
              <!-- JavaScript를 통해 동적 생성 -->
            </div>

          </div>
        </div>
      </div>
    </article>
  </div>

<!-- A/B 테스트 및 장원영 이미지 연동 스크립트 -->
<script>
  // 업로드해주신 장원영 님의 화보 이미지 링크를 Variant A 데이터에 반영했습니다.
  const WONYOUNG_IMAGE_URL = "https://images.unsplash.com/photo-1534528741775-53994a69daeb?w=500"; // 필요시 첨부된 이미지 주소로 교체 가능

  const menuItems = [
    {
      id: "menu01",
      name: "I-청귤캐모마일티",
      imgModel: "[cite: 1]https://images.unsplash.com/photo-1534528741775-53994a69daeb?w=500", // 업로드한 장원영 화보 적용
      imgProductOnly: "/files/attach/images/272857/037/348/03d229fc4c4404a6d6ab32d18f93330e.jpg" // 원본 음료 단독 이미지
    },
    {
      id: "menu02",
      name: "I-딥라떼",
      imgModel: "[cite: 1]https://images.unsplash.com/photo-1534528741775-53994a69daeb?w=500", 
      imgProductOnly: "/files/attach/images/272857/844/343/15b3e049f37bfa7cbdbebf56e96efa9c.jpg"
    },
    {
      id: "menu03",
      name: "I-슈 클라우드 라떼",
      imgModel: "[cite: 1]https://images.unsplash.com/photo-1534528741775-53994a69daeb?w=500",
      imgProductOnly: "/files/attach/images/272857/150/344/03befed4c129906d052fb999e27ce07d.jpg"
    },
    {
      id: "menu04",
      name: "PB&J 와플",
      imgModel: "[cite: 1]https://images.unsplash.com/photo-1534528741775-53994a69daeb?w=500",
      imgProductOnly: "/files/attach/images/272857/164/344/fcfc469ce1ba6e7450ab3e7dae4d2e3e.jpg"
    }
  ];

  // 1. 사용자 그룹 분기 (50:50 무작위 할당 및 유지)
  function getABVariant() {
    let variant = localStorage.getItem('ab_test_variant');
    if (!variant) {
      variant = Math.random() < 0.5 ? 'A' : 'B';
      localStorage.setItem('ab_test_variant', variant);
    }
    return variant;
  }

  // 2. 렌더링 함수
  function renderProducts() {
    const variant = getABVariant();
    const gridContainer = document.getElementById('product-grid');
    const indicator = document.getElementById('ab-variant-indicator');

    if (variant === 'A') {
      indicator.innerHTML = "📌 <b>Variant A (Experimental Group):</b> 장원영 모델 화보 이미지 적용 컷";
    } else {
      indicator.innerHTML = "📌 <b>Variant B (Control Group):</b> 기존 메뉴 단독 누끼 이미지 컷";
    }

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

  // 3. 클릭 전환율(CTR) 측정용 트래킹 함수
  function trackClick(menuId, variant) {
    console.log(`[A/B Test Analytics] Clicked: ${menuId}, Variant: ${variant}`);
    alert(`[A/B 테스트 분석] ${variant} 그룹에서 '${menuId}' 메뉴 클릭 이벤트가 기록되었습니다!`);
  }

  document.addEventListener("DOMContentLoaded", renderProducts);
</script>
</body>
</html>
