<template>
  <div class="main">
    <!-- <button @click="hideLicense()">変換する</button> -->
    <button class="btn btn-gradient" @click="start()">変換する</button>
    <button v-if="success" class="btn btn-gradient" @click="download()">
      一括ダウンロード
    </button>
  </div>
</template>

<script>
export default {
  props: {
    imageData: {
      type: Array,
      default: null,
    },
    imageFiles: {
      type: Array,
      default: null,
    },
  },
  data: () => {
    return {
      textAnnotations: {},
      success: false,
      notApplicableImageIndex: [],
      result: [],
    }
  },
  methods: {
    async start() {
      const imageFiles = this.imageFiles
      if (!imageFiles.length) {
        return
      }
      // console.log({ imageFiles })
      for (let i = 0; i < this.imageFiles[0].length; i++) {
        console.log({ i })
        const resurt = await this.sendAPI(i)
        await this.hideLicense(i, resurt)
      }
      await this.removeTarget()
    },
    // async hideLicense() {
    //   this.success = false
    //   const apiKey = process.env.GOOGLE_API_KEY
    //   // console.log({ process })
    //   // console.log({ apiKey })
    //   const url = `https://vision.googleapis.com/v1/images:annotate?key=${apiKey}`

    //   for (let i = 0; i < this.imageFiles[0].length; i++) {
    //     console.log({ i })
    //     // console.log(this.imageFiles[0])
    //     // console.log({ i })
    //     let file = this.imageFiles[0][i]
    //     console.log('name', file.name)
    //     // console.log({ file })
    //     let filedata = this.imageData[i]
    //     // console.log({ filedata })
    //     const sendBase64Image =
    //       file.type === 'image/jpeg' ? filedata.substr(23) : filedata.substr(22)

    //     // console.log({ sendBase64Image })

    //     const requests = {
    //       requests: [
    //         {
    //           image: {
    //             content: sendBase64Image,
    //           },
    //           features: [
    //             {
    //               type: 'OBJECT_LOCALIZATION',
    //             },
    //             {
    //               type: 'DOCUMENT_TEXT_DETECTION',
    //             },
    //           ],
    //         },
    //       ],
    //     }

    //     const response = await fetch(url, {
    //       method: 'POST',
    //       redirected: true,
    //       body: JSON.stringify(requests),
    //       headers: {
    //         'Content-Type': 'application/json',
    //       },
    //     })

    //     const result = await response.json()
    //     this.result = result
    //     console.log({ result })
    //   }
    // },
    async sendAPI(i) {
      // console.log('発火')
      this.success = false
      const apiKey = process.env.GOOGLE_API_KEY
      // console.log({ process })
      // console.log({ apiKey })
      const url = `https://vision.googleapis.com/v1/images:annotate?key=${apiKey}`

      // console.log(this.imageFiles[0])
      // console.log({ i })
      let file = this.imageFiles[0][i]
      // console.log('name', file.name)
      // console.log({ file })
      let filedata = this.imageData[i][0]
      // console.log({ filedata })
      const sendBase64Image =
        file.type === 'image/jpeg' ? filedata.substr(23) : filedata.substr(22)

      // console.log({ sendBase64Image })

      const requests = {
        requests: [
          {
            image: {
              content: sendBase64Image,
            },
            features: [
              {
                type: 'OBJECT_LOCALIZATION',
              },
              {
                type: 'DOCUMENT_TEXT_DETECTION',
              },
            ],
          },
        ],
      }

      const response = await fetch(url, {
        method: 'POST',
        redirected: true,
        body: JSON.stringify(requests),
        headers: {
          'Content-Type': 'application/json',
        },
      })

      const result = await response.json()
      this.result.push(result)
      // console.log({ result })
      return result
    },
    // async test() {
    async hideLicense(i, result) {
      const previewarea = document.getElementById('previewarea')
      let canvas = previewarea.children[i]
      // console.log({ canvas })
      const ctx = canvas.getContext('2d')

      if ('localizedObjectAnnotations' in result['responses'][0]) {
        // ナンバーの推定結果が存在しているか調べる
        const localizedObjectAnnotations =
          result['responses'][0]['localizedObjectAnnotations']
        // console.log({ localizedObjectAnnotations })
        const licensePlateIndex = localizedObjectAnnotations.findIndex(
          (e) => e.name === 'License plate'
        )
        let textAnnotations = result['responses'][0]['textAnnotations']
        this.textAnnotations = textAnnotations
        // console.log({ licenseText })
        // console.log({ localizedObjectAnnotations })

        // -1 が返ってきたらナンバーが推定されなかった
        // console.log({ licensePlateIndex })
        if (licensePlateIndex !== -1) {
          // バウンディングボックス は左上から時計回りに返ってくる
          // 正則化された値が返ってくるので実際の座標データに変換する
          // とりあえず、左上が分かれば大丈夫なのでその部分だけ取り出す
          const plateTopLeft = {
            x: Math.round(
              canvas.width *
                localizedObjectAnnotations[licensePlateIndex]['boundingPoly'][
                  'normalizedVertices'
                ][0]['x']
            ),
            y: Math.round(
              canvas.height *
                localizedObjectAnnotations[licensePlateIndex]['boundingPoly'][
                  'normalizedVertices'
                ][0]['y']
            ),
          }
          const plateTopRight = {
            x: Math.round(
              canvas.width *
                localizedObjectAnnotations[licensePlateIndex]['boundingPoly'][
                  'normalizedVertices'
                ][1]['x']
            ),
            y: Math.round(
              canvas.height *
                localizedObjectAnnotations[licensePlateIndex]['boundingPoly'][
                  'normalizedVertices'
                ][1]['y']
            ),
          }
          const plateBottomRight = {
            x: Math.round(
              canvas.width *
                localizedObjectAnnotations[licensePlateIndex]['boundingPoly'][
                  'normalizedVertices'
                ][2]['x']
            ),
            y: Math.round(
              canvas.height *
                localizedObjectAnnotations[licensePlateIndex]['boundingPoly'][
                  'normalizedVertices'
                ][2]['y']
            ),
          }
          const plateBottomLeft = {
            x: Math.round(
              canvas.width *
                localizedObjectAnnotations[licensePlateIndex]['boundingPoly'][
                  'normalizedVertices'
                ][3]['x']
            ),
            y: Math.round(
              canvas.height *
                localizedObjectAnnotations[licensePlateIndex]['boundingPoly'][
                  'normalizedVertices'
                ][3]['y']
            ),
          }
          // licensePlateの中にある座標データをtargetとする
          // console.log({ textAnnotations })
          // License plateの座標内にあるデータを１つ取得しtargetとする
          textAnnotations = textAnnotations.reverse()
          let target = {}
          target = textAnnotations.find(
            (e) =>
              e.boundingPoly.vertices[0].x > plateTopLeft.x &&
              e.boundingPoly.vertices[0].y > plateTopLeft.y &&
              e.boundingPoly.vertices[0].x < plateBottomRight.x &&
              e.boundingPoly.vertices[0].y < plateBottomRight.y &&
              e.boundingPoly.vertices[2].x > plateTopLeft.x &&
              e.boundingPoly.vertices[2].y > plateTopLeft.y &&
              e.boundingPoly.vertices[2].x < plateBottomRight.x &&
              e.boundingPoly.vertices[2].y < plateBottomRight.y
          )
          // console.log({ target })

          if (target !== undefined) {
            // ナンバーの座標情報からlicenseplateの四角形に接するように拡大するため各点の移動量を計算
            const textTopLeft = {
              x: target['boundingPoly']['vertices'][0]['x'],
              y: target['boundingPoly']['vertices'][0]['y'],
            }
            const textTopRight = {
              x: target['boundingPoly']['vertices'][1]['x'],
              y: target['boundingPoly']['vertices'][1]['y'],
            }
            const textBottomRight = {
              x: target['boundingPoly']['vertices'][2]['x'],
              y: target['boundingPoly']['vertices'][2]['y'],
            }
            const textBottomLeft = {
              x: target['boundingPoly']['vertices'][3]['x'],
              y: target['boundingPoly']['vertices'][3]['y'],
            }

            const calcTopMove =
              textTopLeft.y > textTopRight.y
                ? textTopRight.y - plateTopLeft.y
                : textTopLeft.y - plateTopLeft.y
            const calcLeftMove =
              textTopLeft.x > textBottomLeft.x
                ? textBottomLeft.x - plateTopLeft.x
                : textTopLeft.x - plateTopLeft.x
            const calcRightMove =
              textTopRight.x > textBottomRight.x
                ? plateTopRight.x - textTopRight.x
                : plateTopRight.x - textBottomRight.x
            const calcBottomMove =
              textBottomRight.y > textBottomLeft.y
                ? plateBottomLeft.y - textBottomRight.y
                : plateBottomLeft.y - textBottomLeft.y

            ctx.fillStyle = 'rgb(255, 255, 255)'
            ctx.beginPath()
            ctx.moveTo(
              textTopLeft.x - calcLeftMove,
              textTopLeft.y - calcTopMove
            )
            ctx.lineTo(
              textTopRight.x + calcRightMove,
              textTopRight.y - calcTopMove
            )
            ctx.lineTo(
              textBottomRight.x + calcRightMove,
              textBottomRight.y + calcBottomMove
            )
            ctx.lineTo(
              textBottomLeft.x - calcLeftMove,
              textBottomLeft.y + calcBottomMove
            )
            ctx.fill()
          }
        } else {
          // console.log('sakujio', i)
          this.notApplicableImageIndex.push(i)
        }
      }
    },
    async removeTarget() {
      const removeTarget = this.notApplicableImageIndex.reverse()
      const previewarea = document.getElementById('previewarea')
      // console.log({ removeTarget })
      for (let i = 0; i < removeTarget.length; i++) {
        const removeTargetDom = previewarea.children[removeTarget[i]]
        // console.log({ removeTargetDom })
        previewarea.removeChild(removeTargetDom)
      }
      if (previewarea.childElementCount > 0) this.success = true
    },
    download() {
      const previewarea = document.getElementById('previewarea')
      const count = previewarea.childElementCount
      for (let i = 0; i < count; i++) {
        let canvas = previewarea.children[i]
        const link = document.createElement('a')
        const downloadBase64Image = canvas.toDataURL('image/png')
        const fileName = `${new Date()
          .toDateString()
          .replace(/\\s+/g, '')}_${Math.round(Math.random() * 1000000000)}.png`
        link.setAttribute('href', downloadBase64Image)
        link.setAttribute('download', fileName)
        link.style.visibility = 'hidden'
        document.body.appendChild(link)
        link.click()
        document.body.removeChild(link)
      }
    },
  },
}
</script>

<style scoped>
.main {
  margin-bottom: 2rem;
}
.btn {
  font-size: 1.2rem;
  font-weight: 700;
  line-height: 1.5;
  position: relative;
  display: inline-block;
  padding: 1rem 2rem;
  cursor: pointer;
  -webkit-user-select: none;
  -moz-user-select: none;
  -ms-user-select: none;
  user-select: none;
  -webkit-transition: all 0.3s;
  transition: all 0.3s;
  text-align: center;
  vertical-align: middle;
  text-decoration: none;
  letter-spacing: 0.1em;
  color: #212529;
  border-radius: 2rem;
}

.btn-gradient {
  color: #fff;
  border: 2px solid #fff;
  border-radius: 2rem;
  background-image: -webkit-gradient(
    linear,
    left top,
    right top,
    from(#fa709a),
    to(#fee140)
  );
  background-image: -webkit-linear-gradient(left, #fa709a 0%, #fee140 100%);
  background-image: linear-gradient(to right, #fa709a 0%, #fee140 100%);
  -webkit-box-shadow: 0 5px 10px rgba(0, 0, 0, 0.1);
  box-shadow: 0 5px 10px rgba(0, 0, 0, 0.1);
}
</style>
