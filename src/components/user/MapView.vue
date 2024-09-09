<template>
  <div class="mt-3"><h5>Your Recorded Gym</h5></div>
  <div class="mt-3">
    <h5>마커를 클릭하면 Record Number가 보여요!!</h5>
  </div>
  <div id="map" style="width: 500px; height: 400px; margin-left: 30%"></div>
</template>

<script setup>
import { onMounted, ref, watch } from "vue";
import { useStore } from "vuex";

const store = useStore();

const latitude = ref(0);
const longitude = ref(0);

// Kakao Maps가 로드되었는지 상태를 추적
const isKakaoLoaded = ref(false);

onMounted(() => {
  if (!("geolocation" in navigator)) {
    alert("위치 정보를 사용할 수 없습니다.");
    return;
  }

  navigator.geolocation.getCurrentPosition(
    (pos) => {
      latitude.value = pos.coords.latitude;
      longitude.value = pos.coords.longitude;

      // Kakao Maps API가 로드되었는지 확인
      if (window.kakao && window.kakao.maps) {
        kakao.maps.load(() => {
          isKakaoLoaded.value = true; // Kakao가 로드되면 상태를 true로 설정
          initMap();
        });
      } else {
        // Kakao Maps API 스크립트 동적 로드
        const script = document.createElement("script");
        script.onload = () => {
          kakao.maps.load(() => {
            isKakaoLoaded.value = true; // Kakao가 로드되면 상태를 true로 설정
            initMap();
          });
        };
        script.src =
          "//dapi.kakao.com/v2/maps/sdk.js?autoload=false&appkey=4efc3df62fa990e9f77005a742caff87&libraries=services";
        document.head.appendChild(script);
        script.async = true;
      }
    },
    (err) => {
      alert(err.message);
    }
  );
});

// 상세 주소로 마커를 추가하는 함수
const addMarkers = (records) => {
  if (!isKakaoLoaded.value) {
    console.warn("Kakao Maps가 아직 로드되지 않았습니다.");
    return; // Kakao Maps가 로드되지 않은 상태에서 함수 실행 중지
  }

  const geocoder = new kakao.maps.services.Geocoder();

  records.forEach((record) => {
    const { addr, id } = record;

    geocoder.addressSearch(addr, function (result, status) {
      if (status === kakao.maps.services.Status.OK) {
        const coords = new kakao.maps.LatLng(result[0].y, result[0].x);

        // 마커 생성
        const marker = new kakao.maps.Marker({
          map: window.mapInstance, // 전역 map 객체 사용
          position: coords,
        });

        // 마커에 id 값을 저장
        marker.customData = { id };

        // 마커 클릭 이벤트 추가
        kakao.maps.event.addListener(marker, "click", function () {
          alert(`Record Number: ${marker.customData.id}`); // 마커 클릭 시 ID 출력
        });

        // 인포윈도우 생성
        const infowindow = new kakao.maps.InfoWindow({
          content: `<div style="width:200px;text-align:center;padding:6px 0;">${addr}</div>`,
        });

        // 인포윈도우 열기
        infowindow.open(window.mapInstance, marker);

        // 지도의 중심을 첫 번째 마커로 이동
        window.mapInstance.setCenter(coords);
      } else {
        console.error(`주소 검색 실패: ${addr}`);
      }
    });
  });
};

const initMap = () => {
  const mapContainer = document.getElementById("map");
  const mapOption = {
    center: new kakao.maps.LatLng(latitude.value, longitude.value),
    level: 3,
  };

  // 지도를 생성합니다
  const map = new kakao.maps.Map(mapContainer, mapOption);
  window.mapInstance = map; // map 객체를 전역으로 설정하여 이후 함수에서 접근할 수 있도록 함

  // 초기 addr 값이 있을 때 마커 추가
  const initialRecords = store.state.recordData;
  if (initialRecords.length > 0) {
    addMarkers(initialRecords);
  }
};

// store의 recordData의 addr을 활용해 지도에 마커 표시
watch(
  () => store.state.recordData,
  (newRecords) => {
    // Kakao가 로드된 이후에만 마커를 추가하도록 설정
    if (isKakaoLoaded.value && newRecords && newRecords.length > 0) {
      addMarkers(newRecords);
    }
  },
  { immediate: true }
);
</script>

<style lang="scss" scoped></style>
