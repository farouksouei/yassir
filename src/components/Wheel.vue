<template>
  <div>
    <!-- type: image -->
    <FortuneWheel
      style="width: 300px; max-width: 100%;"
      ref="wheelEl"
      type="image"
      :useWeight="true"
      :verify="canvasVerify"
      :prizeId="prizeId"
      :angleBase="-2"
      :prizes="prizesImage"
      @rotateStart="onCanvasRotateStart"
      @rotateEnd="onRotateEnd"
    >
      <template #wheel>
        <img src="../assets/roue.png" style="width: 100%;transform: rotateZ(60deg)" />
      </template>
      <template #button>
        <img src="../assets/fleche.png" style="width: 130px; transform: translateY(-20px);"/>
      </template>
    </FortuneWheel>
      
    <p style="color: red;">* Remise valable sur les commandes qui dépassent 10DT, plafonnée à 8DT, et non cumulable avec d'autres promos.</p>
    <button @click="wheelEl.startRotate()" class="styled-button"><h1>  لفْ العجلة 🎉</h1></button>   
  </div>
</template>

<style>
.styled-button {
  background-color: #5C14CD;
  color: white;
  padding: 10px 60px;
  border: none;
  border-radius: 15px;
  cursor: pointer;
  transition: background-color 0.3s;
}

.styled-button:hover {
  background-color: #800080; /* Darker shade of purple on hover */
}

.styled-button:active {
  background-color: #4b0082; /* Even darker shade of purple on button press */
}

/* SweetAlert styles */
.swal2-popup {
  background-color: #5C14CD !important;
  color: white !important;
}

.swal2-title {
  color: white !important;
}

.swal2-content {
  color: white !important;
}

.swal2-icon {
  color: white !important;
}

.swal2-confirm {
  background-color: #5C14CD !important;
  color: white !important;
}

.swal2-cancel {
  background-color: #800080 !important; /* You can change this color if needed */
  color: white !important;
}
</style>

<script setup lang="ts">
import { ref, computed, onMounted } from 'vue'
import FortuneWheel from 'vue-fortune-wheel'
import 'vue-fortune-wheel/style.css'
import sweetalert from 'sweetalert2'

import imagePath from "../assets/button.png";
const prizeId = ref(0)
onMounted(() => {
  sweetalert.fire({
    title: 'لفْ العجلة بإيدك، و انت و نصيبك و الحظ السعيد',
    imageUrl: imagePath, // Replace with the correct path
    confirmButtonText: 'OK',
    imageWidth: 300,
    imageHeight: 200
  }).then((result) => {
    if (result.isConfirmed) {
      console.log('Wheel is ready');
    }
  });
});

const wheelEl = ref()
const canvasVerify = ref(false)
const verifyDuration = 5

const prizesImage = [
  {
    id: 1,
    value: '0%',
    weight: 50
  },
  {
    id: 2,
    value: '50%',
    weight: 1
  },
  {
    id: 3,
    value: '20%',
    weight: 7
  },
  {
    id: 4,
    value: '40%',
    weight: 2
  },
  {
    id: 5,
    value: '15%',
    weight: 10
  },
  {
    id: 6,
    value: '25%',
    weight: 5
  },
  {
    id: 7,
    value: '30%',
    weight: 3
  },
  {
    id: 8,
    value: '10%',
    weight: 22
  },
]

function testRequest(verified, duration) {
  return new Promise((resolve) => {
    setTimeout(() => {
      resolve(verified)
    }, duration)
  })
}

function onCanvasRotateStart(rotate) {
  if (canvasVerify.value) {
    const verified = true // true: the test passed the verification, false: the test failed the verification
    testRequest(verified, verifyDuration * 1000).then((verifiedRes) => {
      if (verifiedRes) {
        console.log('Verification passed, start to rotate')
        rotate() // Call the callback, start spinning
        canvasVerify.value = false // Turn off verification mode
      } else {
        alert('Failed verification')
      }
    })
    return
  }
  console.log('onCanvasRotateStart')
}

function onRotateEnd(prize) {
  const today = new Date()
  const todayString = today.toISOString().split('T')[0]
  const week = coupon_codes.weeks.find(week => week.week === todayString)
  if (week) {
    const coupon = week.coupons.find(coupon => coupon.percentage === prize.value)
    if (coupon) {
      const couponCode = coupon.code;

      // Copy to clipboard
      const el = document.createElement('textarea');
      el.value = couponCode;
      document.body.appendChild(el);
      el.select();
      document.execCommand('copy');
      document.body.removeChild(el);

      sweetalert.fire({
        title: 'ألف مبروك',
        text: 'ألف مبروك إنت كسبت خصم ' + prize.value + ' على طلبك القادم' + ' كود الخصم: ' + couponCode + ' (تم نسخ الكود إلى الحافظة)',
        icon: 'success',
        confirmButtonText: 'Copy code to clipboard'
      }).then((result) => {
        if (result.isConfirmed) {
          copyToClipboard(couponCode)
        }
      })
    } else {
      sweetalert.fire({
        title: 'اليوم ده مش يومك',
        icon: 'error',
        confirmButtonText: 'OK'
      })
    }
  }
}

function copyToClipboard(code) {
  // Create a temporary textarea element
  const el = document.createElement('textarea');

  // Set the value of the textarea to the provided code
  el.value = code;

  // Append the textarea element to the document
  document.body.appendChild(el);

  // Select the text in the textarea
  el.select();

  // Copy the selected text to the clipboard
  document.execCommand('copy');

  // Remove the temporary textarea element
  document.body.removeChild(el);

  // Display a success message
  sweetalert.fire({
    title: 'تم نسخ الكود',
    text: 'تم نسخ الكود إلى الحافظة بنجاح',
    icon: 'success',
    confirmButtonText: 'OK'
  });
}



const coupon_codes = {
  "weeks": [
    {
      "week": "2024-02-10",
      "coupons": [
        {"code": "FG3CC3", "percentage": "10%", "probability": "22%"},
        {"code": "GANRXA", "percentage": "15%", "probability": "10%"},
        {"code": "NB7QTM", "percentage": "20%", "probability": "7%"},
        {"code": "T280IJ", "percentage": "25%", "probability": "5%"},
        {"code": "3H2E77", "percentage": "30%", "probability": "3%"},
        {"code": "BDD5TJ", "percentage": "40%", "probability": "2%"},
        {"code": "QEXYU3", "percentage": "50%", "probability": "1%"}
      ]
    },
    {
      "week": "2024-02-19",
      "coupons": [
        {"code": "0UWUX6", "percentage": "10%", "probability": "22%"},
        {"code": "G0AHGR", "percentage": "15%", "probability": "10%"},
        {"code": "UKYF7B", "percentage": "20%", "probability": "7%"},
        {"code": "UKPLRI", "percentage": "25%", "probability": "5%"},
        {"code": "IU7Z63", "percentage": "30%", "probability": "3%"},
        {"code": "BTR0NX", "percentage": "40%", "probability": "2%"},
        {"code": "VEN8XK", "percentage": "50%", "probability": "1%"}
      ]
    },
    {
      "week": "2023-02-26",
      "coupons": [
        {"code": "KN6430", "percentage": "10%", "probability": "22%"},
        {"code": "9S6611", "percentage": "15%", "probability": "10%"},
        {"code": "8M95JI", "percentage": "20%", "probability": "7%"},
        {"code": "Y6ZM65", "percentage": "25%", "probability": "5%"},
        {"code": "4N8J23", "percentage": "30%", "probability": "3%"},
        {"code": "FG0E4V", "percentage": "40%", "probability": "2%"},
        {"code": "P7N249", "percentage": "50%", "probability": "1%"}
      ]
    },
    {
      "week": "2024-03-04",
      "coupons": [
        {"code": "9W3GDC", "percentage": "10%", "probability": "22%"},
        {"code": "FTQO89", "percentage": "15%", "probability": "10%"},
        {"code": "LZ3OPY", "percentage": "20%", "probability": "7%"},
        {"code": "FQJBS0", "percentage": "25%", "probability": "5%"},
        {"code": "503SBJ", "percentage": "30%", "probability": "3%"},
        {"code": "7KD4FS", "percentage": "40%", "probability": "2%"},
        {"code": "CF9KTO", "percentage": "50%", "probability": "1%"}
      ]
    }
  ]
}

</script>