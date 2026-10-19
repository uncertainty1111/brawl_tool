name: Build Brawl AI APK



on:

  push:

  workflow_dispatch:



permissions:

  contents: write



jobs:

  build-apk:

    runs-on: ubuntu-latest

    steps:

      - name: Checkout repository

        uses: actions/checkout@v4



      - name: Set up JDK 17

        uses: actions/setup-java@v4

        with:

          distribution: 'temurin'

          java-version: '17'



      - name: Generate Android Overlay Project v3.1 (Radial Hold-Drag + Ground-Plane Circle Alignment)

        run: |

          python3 - << 'EOF'

          import os



          os.makedirs("app/src/main/java/com/brawl/aioverlay", exist_ok=True)

          os.makedirs("app/src/main/res/xml", exist_ok=True)



          with open("settings.gradle.kts", "w") as f:

              f.write('''pluginManagement {

              repositories {

                  google()

                  mavenCentral()

                  gradlePluginPortal()

              }

          }

          dependencyResolutionManagement {

              repositoriesMode.set(RepositoriesMode.FAIL_ON_PROJECT_REPOS)

              repositories {

                  google()

                  mavenCentral()

              }

          }

          rootProject.name = "BrawlAiOverlay"

          include(":app")

          ''')



          with open("build.gradle.kts", "w") as f:

              f.write('''plugins {

              id("com.android.application") version "8.2.2" apply false

              id("org.jetbrains.kotlin.android") version "1.9.22" apply false

          }

          ''')



          with open("gradle.properties", "w") as f:

              f.write('''org.gradle.jvmargs=-Xmx2048m -Dfile.encoding=UTF-8

          android.useAndroidX=true

          kotlin.code.style=official

          android.nonTransitiveRClass=true

          ''')



          with open("app/build.gradle.kts", "w") as f:

              f.write('''plugins {

              id("com.android.application")

              id("org.jetbrains.kotlin.android")

          }

          android {

              namespace = "com.brawl.aioverlay"

              compileSdk = 34

              defaultConfig {

                  applicationId = "com.brawl.aioverlay"

                  minSdk = 30

                  targetSdk = 34

                  versionCode = 4

                  versionName = "3.2"

              }

              buildTypes {

                  release {

                      isMinifyEnabled = false

                  }

              }

              compileOptions {

                  sourceCompatibility = JavaVersion.VERSION_17

                  targetCompatibility = JavaVersion.VERSION_17

              }

              kotlinOptions {

                  jvmTarget = "17"

              }

          }

          dependencies {

              implementation("androidx.core:core-ktx:1.12.0")

              implementation("androidx.appcompat:appcompat:1.6.1")

          }

          ''')



          with open("app/src/main/AndroidManifest.xml", "w") as f:

              f.write('''<?xml version="1.0" encoding="utf-8"?>

          <manifest xmlns:android="http://schemas.android.com/apk/res/android">

              <uses-permission android:name="android.permission.SYSTEM_ALERT_WINDOW" />

              <application

                  android:allowBackup="true"

                  android:label="Brawl AI Overlay"

                  android:supportsRtl="true"

                  android:theme="@style/Theme.AppCompat.NoActionBar">

                  <activity

                      android:name=".MainActivity"

                      android:exported="true">

                      <intent-filter>

                          <action android:name="android.intent.action.MAIN" />

                          <category android:name="android.intent.category.LAUNCHER" />

                      </intent-filter>

                  </activity>

                  <service

                      android:name=".BrawlAccessibilityService"

                      android:exported="true"

                      android:label="Brawl AI 패드 자동 조종"

                      android:permission="android.permission.BIND_ACCESSIBILITY_SERVICE">

                      <intent-filter>

                          <action android:name="android.accessibilityservice.AccessibilityService" />

                      </intent-filter>

                      <meta-data

                          android:name="android.accessibilityservice"

                          android:resource="@xml/accessibility_service_config" />

                  </service>

              </application>

          </manifest>

          ''')



          with open("app/src/main/res/xml/accessibility_service_config.xml", "w") as f:

              f.write('''<?xml version="1.0" encoding="utf-8"?>

          <accessibility-service xmlns:android="http://schemas.android.com/apk/res/android"

              android:accessibilityEventTypes="typeAllMask"

              android:accessibilityFeedbackType="feedbackGeneric"

              android:accessibilityFlags="flagDefault"

              android:canPerformGestures="true"

              android:canTakeScreenshot="true"

              android:canRetrieveWindowContent="false"

              android:notificationTimeout="45" />

          ''')



          with open("app/src/main/java/com/brawl/aioverlay/MainActivity.kt", "w") as f:

              f.write('''package com.brawl.aioverlay



          import android.app.Activity

          import android.content.Intent

          import android.graphics.Color

          import android.graphics.drawable.GradientDrawable

          import android.net.Uri

          import android.os.Bundle

          import android.provider.Settings

          import android.view.Gravity

          import android.widget.Button

          import android.widget.LinearLayout

          import android.widget.TextView

          import android.widget.Toast



          class MainActivity : Activity() {

              override fun onCreate(savedInstanceState: Bundle?) {

                  super.onCreate(savedInstanceState)



                  val root = LinearLayout(this).apply {

                      orientation = LinearLayout.VERTICAL

                      gravity = Gravity.CENTER

                      setBackgroundColor(Color.parseColor("#080D1A"))

                      setPadding(60, 60, 60, 60)

                  }



                  val cardBg = GradientDrawable().apply {

                      setColor(Color.parseColor("#111827"))

                      cornerRadius = 40f

                      setStroke(3, Color.parseColor("#38BDF8"))

                  }



                  val card = LinearLayout(this).apply {

                      orientation = LinearLayout.VERTICAL

                      background = cardBg

                      setPadding(56, 56, 56, 56)

                  }



                  val title = TextView(this).apply {

                      text = "BRAWL AI OVERLAY v3.2"

                      textSize = 22f

                      setTextColor(Color.WHITE)

                      setPadding(0, 0, 0, 14)

                  }



                  val sub = TextView(this).apply {

                      text = "• 직선 홀드-드래그(Radial Hold-Drag): 패드를 끝까지 쭉 밀고 유지 후 발사\n• 원터치 방향 고정 스틱 + 자율 카이팅 무빙 (터치 충돌·드래그 끊김 해결)\n• 발밑 원 평면 정렬: 체력바와 발밑 원을 분리해 정확한 적 빨간 원 중심 조준\n• 우측 하단 빨간 공격버튼/UI 마스킹 제외 + 2차 가속도 리드샷 + HP/탄창 관리"

                      textSize = 13f

                      setTextColor(Color.parseColor("#94A3B8"))

                      setPadding(0, 0, 0, 32)

                  }



                  val btn1 = Button(this).apply {

                      text = "1단계: 다른 앱 위에 표시 권한 허용"

                      setOnClickListener {

                          if (!Settings.canDrawOverlays(this@MainActivity)) {

                              startActivity(Intent(Settings.ACTION_MANAGE_OVERLAY_PERMISSION, Uri.parse("package:$packageName")))

                          } else {

                              Toast.makeText(context, "이미 허용되어 있습니다!", Toast.LENGTH_SHORT).show()

                          }

                      }

                  }



                  val btn2 = Button(this).apply {

                      text = "2단계: 접근성 설정에서 [Brawl AI 패드 자동 조종] 켜기"

                      setOnClickListener {

                          startActivity(Intent(Settings.ACTION_ACCESSIBILITY_SETTINGS))

                      }

                  }



                  val btn3 = Button(this).apply {

                      text = "3단계: 왼쪽 위 오버레이 창 띄우기 (실행)"

                      setBackgroundColor(Color.parseColor("#10B981"))

                      setTextColor(Color.BLACK)

                      setOnClickListener {

                          if (!Settings.canDrawOverlays(this@MainActivity)) {

                              Toast.makeText(context, "먼저 1단계 권한을 허용해주세요.", Toast.LENGTH_LONG).show()

                              return@setOnClickListener

                          }

                          val svc = BrawlAccessibilityService.instance

                          if (svc == null) {

                              Toast.makeText(context, "먼저 2단계 접근성 설정에서 [Brawl AI 패드 자동 조종]을 켜주세요!", Toast.LENGTH_LONG).show()

                              startActivity(Intent(Settings.ACTION_ACCESSIBILITY_SETTINGS))

                          } else {

                              svc.showFloatingWindow()

                              Toast.makeText(context, "왼쪽 위에 v3.2 오버레이 창이 떴습니다! 브롤스타즈를 켜세요.", Toast.LENGTH_LONG).show()

                              finish()

                          }

                      }

                  }



                  card.addView(title)

                  card.addView(sub)

                  card.addView(btn1)

                  card.addView(btn2)

                  card.addView(btn3)

                  root.addView(card)

                  setContentView(root)

              }

          }

          ''')



          with open("app/src/main/java/com/brawl/aioverlay/BrawlAccessibilityService.kt", "w") as f:

              f.write('''package com.brawl.aioverlay



          import android.accessibilityservice.AccessibilityService

          import android.accessibilityservice.GestureDescription

          import android.content.Context

          import android.content.res.Resources

          import android.graphics.Bitmap

          import android.graphics.Canvas

          import android.graphics.Color

          import android.graphics.Paint

          import android.graphics.Path

          import android.graphics.PixelFormat

          import android.graphics.drawable.GradientDrawable

          import android.os.Handler

          import android.os.Looper

          import android.view.Display

          import android.view.Gravity

          import android.view.MotionEvent

          import android.view.View

          import android.view.WindowManager

          import android.view.accessibility.AccessibilityEvent

          import android.widget.Button

          import android.widget.LinearLayout

          import android.widget.TextView

          import kotlin.math.abs

          import kotlin.math.atan2

          import kotlin.math.cos

          import kotlin.math.hypot

          import kotlin.math.max

          import kotlin.math.min

          import kotlin.math.sin



          class BrawlAccessibilityService : AccessibilityService() {



              companion object {

                  var instance: BrawlAccessibilityService? = null

              }



              data class ClusterBlob(

                  val cx: Float,

                  val cy: Float,

                  val cellCount: Int,

                  val minX: Float,

                  val maxX: Float,

                  val minY: Float,

                  val maxY: Float

              )



              data class TrackedProjectile(

                  val x: Float,

                  val y: Float,

                  val vx: Float,

                  val vy: Float,

                  val speed: Float

              )



              private var wm: WindowManager? = null

              private var rootOverlay: LinearLayout? = null

              private var radarView: View? = null

              private var statusText: TextView? = null

              private var hpAmmoText: TextView? = null

              private var aiActive = false

              private var minimized = false



              // 원터치 방향 고정 스틱 (손을 떼도 방향이 유지되어 Android 터치 충돌을 원천 차단; 중앙 탭 시 자율 카이팅 모드)

              private var cmdX = 0f

              private var cmdY = 0f

              private var userHoldingOverlay = false



              // 자율 좌우 무빙(Strafe) 위상

              private var strafeSign = 1f

              private var lastStrafeFlipMs = 0L



              // 화면 실제 해상도 (가로 모드 기준 자동 갱신)

              private var realScreenW = 2400f

              private var realScreenH = 1080f



              // 내 브롤러 발밑 초록 원 중심(myFootX, myFootY) 및 이동 속도 벡터

              private var myX = -1f

              private var myY = -1f

              private var myVelX = 0f

              private var myVelY = 0f



              // 내 브롤러 머리 위 HP 비율(0..1) 및 주황색 탄창 칸수(0..3)

              private var myHpRatio = 1.0f

              private var myAmmoBars = 3.0f



              // 가장 가까운 적 브롤러의 발밑 빨간 원 중심 추적 상태

              private var enemyDetected = false

              private var targetEnemyX = -1f

              private var targetEnemyY = -1f

              private var predTargetX = -1f

              private var predTargetY = -1f

              private var enemyVelX = 0f

              private var enemyVelY = 0f

              private var enemyAccX = 0f

              private var enemyAccY = 0f

              private var lastEnemySeenMs = 0L



              // 최신 계산된 좌측 무빙 벡터 & 우측 에임 벡터

              private var latestMoveX = 0f

              private var latestMoveY = 0f

              private var latestAimX = 0f

              private var latestAimY = 0f

              private var latestShouldFire = false



              // 제스처 중복 취소(ACTION_CANCEL) 방지 플래그

              private var gestureBusy = false

              private var lastGestureStartMs = 0L

              private var lastFireMs = 0L



              // 이전 프레임 소형 고속 탄환 블롭 목록 & 밝기 버퍼

              private var prevLuma: IntArray? = null

              private var prevProjBlobs: List<ClusterBlob> = emptyList()

              private var lastFrameTimeMs = 0L



              private val handler = Handler(Looper.getMainLooper())

              private var visionLoopRunning = false

              private var padLoopRunning = false



              override fun onServiceConnected() {

                  super.onServiceConnected()

                  instance = this

                  wm = getSystemService(Context.WINDOW_SERVICE) as WindowManager

                  updateRealScreenSize()

                  showFloatingWindow()

              }



              override fun onAccessibilityEvent(event: AccessibilityEvent?) {}

              override fun onInterrupt() {}



              override fun onDestroy() {

                  super.onDestroy()

                  aiActive = false

                  rootOverlay?.let {

                      try { wm?.removeView(it) } catch (_: Throwable) {}

                  }

                  rootOverlay = null

                  if (instance === this) instance = null

              }



              private fun updateRealScreenSize() {

                  val dm = Resources.getSystem().displayMetrics

                  val w = max(dm.widthPixels, dm.heightPixels).toFloat()

                  val h = min(dm.widthPixels, dm.heightPixels).toFloat()

                  if (w > 400f && h > 300f) {

                      realScreenW = w

                      realScreenH = h

                  }

              }



              fun showFloatingWindow() {

                  rootOverlay?.let {

                      try { wm?.removeView(it) } catch (_: Throwable) {}

                      rootOverlay = null

                  }



                  val params = WindowManager.LayoutParams(

                      430,

                      WindowManager.LayoutParams.WRAP_CONTENT,

                      WindowManager.LayoutParams.TYPE_APPLICATION_OVERLAY,

                      WindowManager.LayoutParams.FLAG_NOT_FOCUSABLE or WindowManager.LayoutParams.FLAG_LAYOUT_IN_SCREEN,

                      PixelFormat.TRANSLUCENT

                  ).apply {

                      gravity = Gravity.TOP or Gravity.START

                      x = 20

                      y = 20

                  }



                  val windowBg = GradientDrawable().apply {

                      setColor(Color.parseColor("#E60F172A"))

                      cornerRadius = 36f

                      setStroke(3, Color.parseColor("#38BDF8"))

                  }



                  val container = LinearLayout(this).apply {

                      orientation = LinearLayout.VERTICAL

                      background = windowBg

                      setPadding(22, 16, 22, 20)

                  }



                  val header = LinearLayout(this).apply {

                      orientation = LinearLayout.HORIZONTAL

                      gravity = Gravity.CENTER_VERTICAL

                      setPadding(4, 0, 4, 10)

                  }



                  val titleTv = TextView(this).apply {

                      text = "● BRAWL AI v3.2 (상단바 드래그)"

                      textSize = 11f

                      setTextColor(Color.parseColor("#38BDF8"))

                      layoutParams = LinearLayout.LayoutParams(0, LinearLayout.LayoutParams.WRAP_CONTENT, 1f)

                  }



                  val btnMin = Button(this).apply {

                      text = "—"

                      textSize = 11f

                      setPadding(8, 2, 8, 2)

                      setBackgroundColor(Color.parseColor("#1E293B"))

                      setTextColor(Color.WHITE)

                  }



                  header.addView(titleTv)

                  header.addView(btnMin)



                  var startX = 0

                  var startY = 0

                  var touchX = 0f

                  var touchY = 0f

                  header.setOnTouchListener { _, ev ->

                      when (ev.action) {

                          MotionEvent.ACTION_DOWN -> {

                              userHoldingOverlay = true

                              startX = params.x

                              startY = params.y

                              touchX = ev.rawX

                              touchY = ev.rawY

                              true

                          }

                          MotionEvent.ACTION_MOVE -> {

                              params.x = startX + (ev.rawX - touchX).toInt()

                              params.y = startY + (ev.rawY - touchY).toInt()

                              wm?.updateViewLayout(container, params)

                              true

                          }

                          MotionEvent.ACTION_UP, MotionEvent.ACTION_CANCEL -> {

                              userHoldingOverlay = false

                              true

                          }

                          else -> false

                      }

                  }



                  val body = LinearLayout(this).apply {

                      orientation = LinearLayout.VERTICAL

                  }



                  val toggleBgOn = GradientDrawable().apply {

                      setColor(Color.parseColor("#10B981"))

                      cornerRadius = 22f

                  }

                  val toggleBgOff = GradientDrawable().apply {

                      setColor(Color.parseColor("#334155"))

                      cornerRadius = 22f

                  }



                  val btnToggle = Button(this).apply {

                      text = "직접 조종: OFF (누르면 시작)"

                      textSize = 11.5f

                      setTextColor(Color.WHITE)

                      background = toggleBgOff

                      setOnClickListener {

                          aiActive = !aiActive

                          if (aiActive) {

                              updateRealScreenSize()

                              text = "● 직접 조종: ON (누르면 멈춤)"

                              setTextColor(Color.BLACK)

                              background = toggleBgOn

                              startEngines()

                          } else {

                              text = "직접 조종: OFF (수동 모드)"

                              setTextColor(Color.WHITE)

                              background = toggleBgOff

                              statusText?.text = "상태: 수동 조작 대기 중"

                          }

                      }

                  }



                  // 방향 지시 조이스틱 + 실시간 인식 미니 레이더 통합 뷰

                  // 중요: 스틱을 한번 터치하고 손을 떼도 방향이 유지되므로 Android 터치 충돌 없이 패드를 100% 연속 드래그합니다!

                  val stickAndRadar = object : View(this) {

                      private val ringPaint = Paint(Paint.ANTI_ALIAS_FLAG).apply {

                          style = Paint.Style.STROKE

                          strokeWidth = 4.5f

                          color = Color.parseColor("#38BDF8")

                      }

                      private val bgPaint = Paint(Paint.ANTI_ALIAS_FLAG).apply {

                          style = Paint.Style.FILL

                          color = Color.parseColor("#1E293B")

                      }

                      private val knobPaint = Paint(Paint.ANTI_ALIAS_FLAG).apply {

                          style = Paint.Style.FILL

                          color = Color.parseColor("#10B981")

                      }

                      private val moveVecPaint = Paint(Paint.ANTI_ALIAS_FLAG).apply {

                          style = Paint.Style.STROKE

                          strokeWidth = 6f

                          color = Color.parseColor("#34D399")

                      }

                      private val aimVecPaint = Paint(Paint.ANTI_ALIAS_FLAG).apply {

                          style = Paint.Style.STROKE

                          strokeWidth = 5f

                          color = Color.parseColor("#F43F5E")

                      }

                      private val enemyDotPaint = Paint(Paint.ANTI_ALIAS_FLAG).apply {

                          style = Paint.Style.FILL

                          color = Color.parseColor("#EF4444")

                      }

                      private val leadDotPaint = Paint(Paint.ANTI_ALIAS_FLAG).apply {

                          style = Paint.Style.FILL

                          color = Color.parseColor("#FBBF24")

                      }



                      override fun onDraw(canvas: Canvas) {

                          super.onDraw(canvas)

                          val cx = width / 2f

                          val cy = height / 2f

                          val r = min(cx, cy) - 12f

                          canvas.drawCircle(cx, cy, r, bgPaint)

                          canvas.drawCircle(cx, cy, r, ringPaint)



                          // AI가 실제로 드래그 중인 무빙 방향(초록선) & 조준 방향(빨간선) 실시간 표시

                          if (aiActive) {

                              canvas.drawLine(cx, cy, cx + latestMoveX * (r - 14f), cy + latestMoveY * (r - 14f), moveVecPaint)

                              if (enemyDetected) {

                                  canvas.drawLine(cx, cy, cx + latestAimX * (r - 10f), cy + latestAimY * (r - 10f), aimVecPaint)

                                  val relEx = ((targetEnemyX - myX) / (realScreenW * 0.45f)).coerceIn(-1f, 1f)

                                  val relEy = ((targetEnemyY - myY) / (realScreenH * 0.45f)).coerceIn(-1f, 1f)

                                  canvas.drawCircle(cx + relEx * (r - 16f), cy + relEy * (r - 16f), 9f, enemyDotPaint)



                                  val relPx = ((predTargetX - myX) / (realScreenW * 0.45f)).coerceIn(-1f, 1f)

                                  val relPy = ((predTargetY - myY) / (realScreenH * 0.45f)).coerceIn(-1f, 1f)

                                  canvas.drawCircle(cx + relPx * (r - 16f), cy + relPy * (r - 16f), 6f, leadDotPaint)

                              }

                          }



                          val kx = cx + cmdX * (r - 20f)

                          val ky = cy + cmdY * (r - 20f)

                          canvas.drawCircle(kx, ky, 18f, knobPaint)

                      }



                      override fun onTouchEvent(event: MotionEvent): Boolean {

                          val cx = width / 2f

                          val cy = height / 2f

                          when (event.action) {

                              MotionEvent.ACTION_DOWN, MotionEvent.ACTION_MOVE -> {

                                  userHoldingOverlay = true

                                  val dx = event.x - cx

                                  val dy = event.y - cy

                                  val d = hypot(dx, dy)

                                  if (d < 22f) {

                                      cmdX = 0f

                                      cmdY = 0f

                                  } else {

                                      cmdX = dx / d

                                      cmdY = dy / d

                                  }

                                  invalidate()

                              }

                              MotionEvent.ACTION_UP, MotionEvent.ACTION_CANCEL -> {

                                  userHoldingOverlay = false

                                  invalidate()

                              }

                          }

                          return true

                      }

                  }.apply {

                      layoutParams = LinearLayout.LayoutParams(200, 200).apply {

                          gravity = Gravity.CENTER_HORIZONTAL

                          topMargin = 10

                          bottomMargin = 8

                      }

                  }

                  radarView = stickAndRadar



                  val btnAutoMove = Button(this).apply {

                      text = "중앙 리셋 (AI 자율 카이팅 무빙)"

                      textSize = 10f

                      setPadding(6, 4, 6, 4)

                      setBackgroundColor(Color.parseColor("#1E293B"))

                      setTextColor(Color.parseColor("#38BDF8"))

                      setOnClickListener {

                          cmdX = 0f

                          cmdY = 0f

                          radarView?.invalidate()

                      }

                  }



                  val hpTv = TextView(this).apply {

                      text = "HP: 100% | 탄창: 3.0칸"

                      textSize = 10.5f

                      gravity = Gravity.CENTER

                      setTextColor(Color.parseColor("#34D399"))

                      setPadding(0, 6, 0, 2)

                  }

                  hpAmmoText = hpTv



                  val stTv = TextView(this).apply {

                      text = "초록선:무빙드래그 / 빨간선:에임조준"

                      textSize = 9.5f

                      gravity = Gravity.CENTER

                      setTextColor(Color.parseColor("#CBD5E1"))

                  }

                  statusText = stTv



                  btnMin.setOnClickListener {

                      minimized = !minimized

                      body.visibility = if (minimized) View.GONE else View.VISIBLE

                      btnMin.text = if (minimized) "□" else "—"

                  }



                  body.addView(btnToggle)

                  body.addView(stickAndRadar)

                  body.addView(btnAutoMove)

                  body.addView(hpTv)

                  body.addView(stTv)



                  container.addView(header)

                  container.addView(body)



                  rootOverlay = container

                  wm?.addView(container, params)

              }



              private fun startEngines() {

                  lastFrameTimeMs = System.currentTimeMillis()

                  if (!visionLoopRunning) {

                      visionLoopRunning = true

                      scheduleVisionCapture()

                  }

                  if (!padLoopRunning) {

                      padLoopRunning = true

                      schedulePadDragLoop()

                  }

              }



              // 1) 화면 인식 루프 (안드로이드 스크린샷 API 쿨타임을 준수하며 초록/파랑/빨강 원 및 탄환 스캔)

              private fun scheduleVisionCapture() {

                  if (!aiActive) {

                      visionLoopRunning = false

                      return

                  }

                  takeScreenshot(

                      Display.DEFAULT_DISPLAY,

                      mainExecutor,

                      object : TakeScreenshotCallback {

                          override fun onSuccess(screenshot: ScreenshotResult) {

                              try {

                                  val hw = screenshot.hardwareBuffer

                                  val bmp = Bitmap.wrapHardwareBuffer(hw, screenshot.colorSpace)

                                      ?.copy(Bitmap.Config.ARGB_8888, false)

                                  hw.close()

                                  if (bmp != null) {

                                      processScreenFrame(bmp)

                                      bmp.recycle()

                                  }

                              } catch (_: Throwable) {

                              }

                              handler.postDelayed({ scheduleVisionCapture() }, 230L)

                          }



                          override fun onFailure(errorCode: Int) {

                              extrapolateBetweenFrames(0.13f)

                              handler.postDelayed({ scheduleVisionCapture() }, 150L)

                          }

                      }

                  )

              }



              // 2) 패드 홀드-드래그 주입 루프 (스크린샷 주기와 독립적으로 패드를 끊김 없이 쭉 밀어줌)

              private fun schedulePadDragLoop() {

                  if (!aiActive) {

                      padLoopRunning = false

                      return

                  }

                  val now = System.currentTimeMillis()

                  if (gestureBusy && (now - lastGestureStartMs) > 420L) {

                      gestureBusy = false

                  }



                  if (!userHoldingOverlay && !gestureBusy) {

                      var mx = latestMoveX

                      var my = latestMoveY

                      if (hypot(mx, my) < 0.15f) {

                          if (now - lastStrafeFlipMs > 950L) {

                              strafeSign = -strafeSign

                              lastStrafeFlipMs = now

                          }

                          mx = if (cmdX != 0f || cmdY != 0f) cmdX else (0.78f * strafeSign)

                          my = if (cmdX != 0f || cmdY != 0f) cmdY else (0.32f * strafeSign)

                          val len = hypot(mx, my).coerceAtLeast(1f)

                          mx /= len

                          my /= len

                          latestMoveX = mx

                          latestMoveY = my

                      }



                      val canFireNow = latestShouldFire && enemyDetected &&

                          (now - lastEnemySeenMs) < 900L && (now - lastFireMs) > 500L



                      dispatchHoldDragPadGesture(

                          mx, my,

                          latestAimX, latestAimY,

                          canFireNow

                      )

                      if (canFireNow) {

                          lastFireMs = now

                      }

                      radarView?.invalidate()

                  }



                  handler.postDelayed({ schedulePadDragLoop() }, 55L)

              }



              private fun extrapolateBetweenFrames(dt: Float) {

                  if (!enemyDetected || targetEnemyX < 0f) return

                  val now = System.currentTimeMillis()

                  if (now - lastEnemySeenMs > 900L) {

                      enemyDetected = false

                      latestShouldFire = false

                      return

                  }

                  targetEnemyX = (targetEnemyX + enemyVelX * dt).coerceIn(realScreenW * 0.1f, realScreenW * 0.9f)

                  targetEnemyY = (targetEnemyY + enemyVelY * dt).coerceIn(realScreenH * 0.1f, realScreenH * 0.9f)

                  updateAimAndMoveVectors()

              }



              private fun processScreenFrame(bmp: Bitmap) {

                  val nowMs = System.currentTimeMillis()

                  val dt = ((nowMs - lastFrameTimeMs).coerceIn(80L, 450L)) / 1000f

                  lastFrameTimeMs = nowMs



                  val w = bmp.width

                  val h = bmp.height

                  realScreenW = w.toFloat()

                  realScreenH = h.toFloat()



                  val step = max(4, w / 160)

                  val gridW = w / step

                  val gridH = h / step

                  val currLuma = IntArray(gridW * gridH)

                  val greenMask = BooleanArray(gridW * gridH)

                  val blueMask = BooleanArray(gridW * gridH)

                  val redMask = BooleanArray(gridW * gridH)

                  val fastMask = BooleanArray(gridW * gridH)



                  val oldLuma = prevLuma



                  for (gy in 2 until gridH - 2) {

                      val py = gy * step

                      for (gx in 2 until gridW - 2) {

                          val px = gx * step



                          // [UI 및 패드 영역 철저히 마스킹 제외]

                          // 1) 상단 스코어/시간 UI 제외

                          if (py < h * 0.12f) continue

                          // 2) 좌측 상단 오버레이 창 제외

                          if (px < w * 0.28f && py < h * 0.56f) continue

                          // 3) 좌측 하단 무빙 패드 영역 제외

                          if (px < w * 0.30f && py > h * 0.52f) continue

                          // 4) 우측 하단 빨간 공격 조이스틱 / 궁극기 / 가젯 버튼 영역 완벽 제외!

                          if (px > w * 0.65f && py > h * 0.44f) continue



                          val c = bmp.getPixel(px, py)

                          val r = Color.red(c)

                          val g = Color.green(c)

                          val b = Color.blue(c)

                          val luma = (r + g + b) / 3

                          val idx = gy * gridW + gx

                          currLuma[idx] = luma



                          // (A) 내 브롤러 초록색(발밑 초록 원 & 머리 위 초록 HP바) 후보

                          if (px in (w * 0.35f).toInt()..(w * 0.65f).toInt() &&

                              py in (h * 0.28f).toInt()..(h * 0.68f).toInt()) {

                              if (g > 170 && r < 115 && b < 135 && (g - r) > 65 && (g - b) > 45) {

                                  greenMask[idx] = true

                              }

                          }



                          // (B) 아군/분신 파랑·하늘색 원 감지 (타겟 제외용)

                          if (b > 170 && g > 115 && r < 100 && (b - r) > 80) {

                              blueMask[idx] = true

                          }



                          // (C) 적 브롤러 빨간 원 / 빨간 체력바 감지

                          if (r > 185 && g < 78 && b < 90 && (r - g) > 115 && (r - b) > 100) {

                              redMask[idx] = true

                          }



                          // (D) 프레임 간 급격히 움직이는 고휘도 탄환 픽셀

                          if (oldLuma != null && oldLuma.size == currLuma.size) {

                              val diff = abs(luma - oldLuma[idx])

                              if (diff > 60 && (r > 195 || g > 195 || b > 195)) {

                                  fastMask[idx] = true

                              }

                          }

                      }

                  }

                  prevLuma = currLuma



                  // 1. [발밑 초록 원 평면 정렬]

                  // 내 브롤러의 초록 HP바(머리 위)와 발밑 초록 원을 구분하여 내 발밑 중심(myX, myY)을 정확히 산출!

                  val greenClusters = extractClusters(greenMask, gridW, gridH, step, 2, 120)

                  var detectedFootX = w * 0.50f

                  var detectedFootY = h * 0.535f

                  var hpBarFound = false



                  for (gc in greenClusters) {

                      // 해당 초록 클러스터 바로 아래(0.015h ~ 0.032h)에 주황색 3칸 탄창 바가 있는지 확인

                      if (hasOrangeAmmoUnderneath(bmp, gc.cx, gc.maxY, w, h)) {

                          // 이 클러스터는 100% 내 브롤러 머리 위 초록 HP바!

                          scanMyHpAndAmmoFromBar(bmp, gc.cx, gc.cy, w, h)

                          // 내 발밑 초록 원 중심은 머리 위 HP바에서 정확히 아래로 0.108 * h 지점!

                          detectedFootX = gc.cx.coerceIn(w * 0.40f, w * 0.60f)

                          detectedFootY = (gc.cy + h * 0.108f).coerceIn(h * 0.44f, h * 0.62f)

                          hpBarFound = true

                          break

                      }

                  }



                  if (!hpBarFound) {

                      // 발밑 초록 원 클러스터(화면 중앙 하단부) 직접 탐색

                      val footGreen = greenClusters.filter { it.cy in (h * 0.46f)..(h * 0.64f) }

                          .minByOrNull { hypot(it.cx - w * 0.50f, it.cy - h * 0.54f) }

                      if (footGreen != null) {

                          detectedFootX = footGreen.cx

                          detectedFootY = footGreen.cy

                      }

                      scanMyHpAndAmmoFromBar(bmp, detectedFootX, detectedFootY - h * 0.108f, w, h)

                  }



                  val myMaxSpeedPx = w * 0.18f

                  if (myX >= 0f) {

                      val padVx = latestMoveX * myMaxSpeedPx

                      val padVy = latestMoveY * myMaxSpeedPx

                      myVelX = 0.5f * myVelX + 0.5f * padVx

                      myVelY = 0.5f * myVelY + 0.5f * padVy

                  }

                  myX = detectedFootX

                  myY = detectedFootY



                  // 2. 아군 파란 원 클러스터 추출

                  val blueClusters = extractClusters(blueMask, gridW, gridH, step, 3, 120)



                  // 3. [적 발밑 빨간 원 평면 정렬 & 개별 클러스터 락온]

                  // 적의 머리 위 빨간 HP바와 발밑 빨간 원을 수직 그룹핑하여 항상 '발밑 빨간 원 중심'으로 조준점을 통일!

                  val rawRedClusters = extractClusters(redMask, gridW, gridH, step, 3, 150)

                      .filter { rc ->

                          val notBlue = blueClusters.none { bc -> hypot(rc.cx - bc.cx, rc.cy - bc.cy) < w * 0.055f }

                          val distMe = hypot(rc.cx - myX, rc.cy - myY)

                          notBlue && distMe > w * 0.045f && distMe < w * 0.48f

                      }



                  val enemyFootPositions = resolveEnemyFootCircles(rawRedClusters, w, h)



                  val lockedFoot = if (enemyDetected && targetEnemyX >= 0f) {

                      enemyFootPositions.minByOrNull { pt ->

                          val distPrev = hypot(pt.first - targetEnemyX, pt.second - targetEnemyY)

                          val distMe = hypot(pt.first - myX, pt.second - myY)

                          distPrev * 0.65f + distMe * 0.35f

                      }

                  } else {

                      enemyFootPositions.minByOrNull { pt -> hypot(pt.first - myX, pt.second - myY) }

                  }



                  if (lockedFoot != null) {

                      val ex = lockedFoot.first

                      val ey = lockedFoot.second



                      if (enemyDetected && targetEnemyX >= 0f) {

                          val jumpDist = hypot(ex - targetEnemyX, ey - targetEnemyY)

                          if (jumpDist < w * 0.20f) {

                              val rawVx = ((ex - targetEnemyX) / dt + myVelX * 0.2f).coerceIn(-w * 0.25f, w * 0.25f)

                              val rawVy = ((ey - targetEnemyY) / dt + myVelY * 0.2f).coerceIn(-h * 0.25f, h * 0.25f)

                              val newVx = enemyVelX * 0.62f + rawVx * 0.38f

                              val newVy = enemyVelY * 0.62f + rawVy * 0.38f



                              val rawAx = ((newVx - enemyVelX) / dt).coerceIn(-w * 0.30f, w * 0.30f)

                              val rawAy = ((newVy - enemyVelY) / dt).coerceIn(-h * 0.30f, h * 0.30f)

                              enemyAccX = enemyAccX * 0.72f + rawAx * 0.28f

                              enemyAccY = enemyAccY * 0.72f + rawAy * 0.28f



                              enemyVelX = newVx

                              enemyVelY = newVy

                          } else {

                              enemyVelX = 0f

                              enemyVelY = 0f

                              enemyAccX = 0f

                              enemyAccY = 0f

                          }

                      }

                      targetEnemyX = ex

                      targetEnemyY = ey

                      enemyDetected = true

                      lastEnemySeenMs = nowMs

                  } else if (nowMs - lastEnemySeenMs > 850L) {

                      enemyDetected = false

                  }



                  // 4. 소형 고속 탄환 클러스터 추출 및 직선 궤도 회피 계산

                  val currProjBlobs = extractClusters(fastMask, gridW, gridH, step, 2, 20)

                  val projectiles = matchAndTrackProjectiles(currProjBlobs, prevProjBlobs, dt, w)

                  prevProjBlobs = currProjBlobs



                  val dodgePair = computeRelativeTrajectoryDodge(projectiles, myX, myY, myVelX, myVelY, myMaxSpeedPx, w)

                  updateAimAndMoveVectors(dodgePair.first, dodgePair.second)

              }



              // 적의 빨간 클러스터들을 분석해 머리 위 빨간 체력바가 아닌 '발밑 빨간 원 중심(Foot Circle)' 좌표 리스트로 변환

              private fun resolveEnemyFootCircles(

                  redClusters: List<ClusterBlob>,

                  w: Int,

                  h: Int

              ): List<Pair<Float, Float>> {

                  if (redClusters.isEmpty()) return emptyList()

                  val used = BooleanArray(redClusters.size)

                  val footPoints = mutableListOf<Pair<Float, Float>>()



                  for (i in redClusters.indices) {

                      if (used[i]) continue

                      used[i] = true

                      val c1 = redClusters[i]

                      var groupMinYCluster = c1

                      var groupMaxYCluster = c1



                      // 같은 X열(±0.05w) 및 브롤러 키 범위(±0.16h) 내에 있는 빨간 클러스터(체력바 + 발밑 빨간 원) 묶기

                      for (j in redClusters.indices) {

                          if (used[j]) continue

                          val c2 = redClusters[j]

                          if (abs(c1.cx - c2.cx) < w * 0.055f && abs(c1.cy - c2.cy) < h * 0.16f) {

                              used[j] = true

                              if (c2.cy < groupMinYCluster.cy) groupMinYCluster = c2

                              if (c2.cy > groupMaxYCluster.cy) groupMaxYCluster = c2

                          }

                      }



                      if (groupMaxYCluster !== groupMinYCluster &&

                          (groupMaxYCluster.cy - groupMinYCluster.cy) > h * 0.045f) {

                          // 위쪽은 체력바, 아래쪽(groupMaxYCluster)은 발밑 빨간 원!

                          footPoints.add(Pair(groupMaxYCluster.cx, groupMaxYCluster.cy))

                      } else {

                          // 단일 클러스터만 잡힌 경우: 납작한 막대(높이가 좁음)면 체력바이므로 +0.105h 내려서 발밑 원 위치로 보정!

                          val clusterHeight = c1.maxY - c1.minY

                          val clusterWidth = c1.maxX - c1.minX

                          if (clusterHeight < h * 0.026f && clusterWidth > clusterHeight * 1.4f) {

                              footPoints.add(Pair(c1.cx, (c1.cy + h * 0.105f).coerceAtMost(h * 0.90f)))

                          } else {

                              footPoints.add(Pair(c1.cx, c1.cy))

                          }

                      }

                  }

                  return footPoints

              }



              private fun hasOrangeAmmoUnderneath(bmp: Bitmap, cx: Float, barBottomY: Float, w: Int, h: Int): Boolean {

                  val ammoY = (barBottomY + h * 0.016f).toInt().coerceIn(10, h - 15)

                  val halfW = (w * 0.025f).toInt().coerceAtLeast(16)

                  val startX = (cx - halfW).toInt().coerceIn(10, w - 20)

                  val endX = (cx + halfW).toInt().coerceIn(startX + 1, w - 10)

                  var orangeCnt = 0

                  var x = startX

                  while (x <= endX) {

                      val c = bmp.getPixel(x, ammoY)

                      val r = Color.red(c)

                      val g = Color.green(c)

                      val b = Color.blue(c)

                      if (r > 195 && g in 80..220 && b < 88) {

                          orangeCnt++

                      }

                      x += 4

                  }

                  return orangeCnt >= 2

              }



              private fun updateAimAndMoveVectors(dodgeX: Float = 0f, dodgeY: Float = 0f) {

                  val underThreat = hypot(dodgeX, dodgeY) > 0.08f

                  val nowMs = System.currentTimeMillis()



                  var enemyDist = 9999f

                  if (enemyDetected && targetEnemyX >= 0f) {

                      enemyDist = hypot(targetEnemyX - myX, targetEnemyY - myY)

                      val bulletSpeedPx = realScreenW * 0.60f

                      val tHit = (enemyDist / bulletSpeedPx).coerceIn(0f, 0.50f)



                      var leadDx = enemyVelX * tHit + 0.5f * enemyAccX * tHit * tHit

                      var leadDy = enemyVelY * tHit + 0.5f * enemyAccY * tHit * tHit



                      // 리드샷 예측 폭이 적 거리의 30%를 넘지 않도록 제한 (조준선이 엉뚱한 곳으로 튀는 현상 방지)

                      val maxLeadOffset = (enemyDist * 0.30f).coerceAtMost(realScreenW * 0.10f)

                      val leadLen = hypot(leadDx, leadDy)

                      if (leadLen > maxLeadOffset && leadLen > 1f) {

                          leadDx = (leadDx / leadLen) * maxLeadOffset

                          leadDy = (leadDy / leadLen) * maxLeadOffset

                      }



                      predTargetX = targetEnemyX + leadDx

                      predTargetY = targetEnemyY + leadDy



                      // 내 발밑 초록 원(myX, myY)에서 적 발밑 빨간 원 예측점(predTargetX, predTargetY)으로 정확한 조준 각도 산출

                      val aimAng = atan2(predTargetY - myY, predTargetX - myX)

                      latestAimX = cos(aimAng)

                      latestAimY = sin(aimAng)



                      val lowHpHoldFire = myHpRatio < 0.38f && enemyDist > realScreenW * 0.16f

                      val hasAmmo = myAmmoBars >= 0.85f

                      latestShouldFire = !lowHpHoldFire && hasAmmo && enemyDist < realScreenW * 0.46f

                  } else {

                      latestShouldFire = false

                  }



                  if (nowMs - lastStrafeFlipMs > 950L) {

                      strafeSign = -strafeSign

                      lastStrafeFlipMs = nowMs

                  }



                  var baseMoveX = cmdX

                  var baseMoveY = cmdY



                  if (enemyDetected && enemyDist in 10f..(realScreenW * 0.48f)) {

                      val toEnemyUx = (targetEnemyX - myX) / enemyDist

                      val toEnemyUy = (targetEnemyY - myY) / enemyDist

                      val perpUx = -toEnemyUy * strafeSign

                      val perpUy = toEnemyUx * strafeSign



                      if (myHpRatio < 0.40f) {

                          baseMoveX = -toEnemyUx * 0.75f + perpUx * 0.45f

                          baseMoveY = -toEnemyUy * 0.75f + perpUy * 0.45f

                      } else if (hypot(cmdX, cmdY) < 0.15f) {

                          val idealDist = realScreenW * 0.25f

                          val radialFactor = ((enemyDist - idealDist) / (realScreenW * 0.15f)).coerceIn(-0.85f, 0.85f)

                          baseMoveX = toEnemyUx * radialFactor * 0.65f + perpUx * 0.75f

                          baseMoveY = toEnemyUy * radialFactor * 0.65f + perpUy * 0.75f

                      }

                  } else if (hypot(baseMoveX, baseMoveY) < 0.15f) {

                      baseMoveX = 0.82f * strafeSign

                      baseMoveY = 0.25f * strafeSign

                  }



                  var finalX = if (underThreat) (dodgeX * 0.85f + baseMoveX * 0.15f) else baseMoveX

                  var finalY = if (underThreat) (dodgeY * 0.85f + baseMoveY * 0.15f) else baseMoveY

                  val mLen = hypot(finalX, finalY)

                  if (mLen > 0.05f) {

                      finalX /= mLen

                      finalY /= mLen

                  }



                  latestMoveX = finalX

                  latestMoveY = finalY



                  val hpPct = (myHpRatio * 100).toInt().coerceIn(5, 100)

                  hpAmmoText?.text = "HP: ${hpPct}% | 탄창: ${String.format("%.1f", myAmmoBars)}칸"

                  hpAmmoText?.setTextColor(

                      if (hpPct < 40) Color.parseColor("#FB7185") else Color.parseColor("#34D399")

                  )



                  statusText?.text = when {

                      underThreat -> "⚡ 고속탄 감지! 직선 궤도 수직 회피 드래그"

                      hpPct < 40 -> "🩹 저체력 회복 관리 (사격 중지·후퇴 드래그)"

                      enemyDetected && latestShouldFire -> "🎯 적 발밑 빨간원 락온! 리드샷 조준·발사"

                      enemyDetected -> "🕹️ 적 추적 + 거리유지 카이팅 드래그"

                      else -> "🕹️ 패드 연속 홀드-드래그 무빙 작동 중"

                  }

              }



              // [핵심 해결] 조준 각도 흔들림이 전혀 없는 동일 선상(Radial) 홀드-드래그 경로 생성

              // (0.90R <-> 1.04R 구간을 같은 각도로 유지한 뒤 마지막에 바깥쪽 1.04R로 밀며 손을 떼므로 사격 취소가 절대 발생하지 않음)

              private fun buildRadialHoldDragPath(

                  startX: Float,

                  startY: Float,

                  ux: Float,

                  uy: Float,

                  dragR: Float,

                  holdLoops: Int

              ): Path {

                  val path = Path()

                  path.moveTo(startX, startY)



                  val innerX = startX + ux * (dragR * 0.90f)

                  val innerY = startY + uy * (dragR * 0.90f)

                  val outerX = startX + ux * (dragR * 1.04f)

                  val outerY = startY + uy * (dragR * 1.04f)



                  path.lineTo(outerX, outerY)

                  for (i in 0 until holdLoops) {

                      path.lineTo(innerX, innerY)

                      path.lineTo(outerX, outerY)

                  }

                  return path

              }



              // 좌측 무빙 패드 & 우측 에임 패드 홀드-드래그 제스처 주입

              private fun dispatchHoldDragPadGesture(

                  mx: Float, my: Float,

                  ax: Float, ay: Float,

                  fire: Boolean

              ) {

                  if (!aiActive || gestureBusy) return



                  val leftPadX = realScreenW * 0.15f

                  val leftPadY = realScreenH * 0.76f

                  val moveDragR = realScreenH * 0.145f



                  val rightPadX = realScreenW * 0.84f

                  val rightPadY = realScreenH * 0.74f

                  val aimDragR = realScreenH * 0.165f



                  val builder = GestureDescription.Builder()

                  var added = false



                  val mLen = hypot(mx, my)

                  if (mLen > 0.1f) {

                      val mux = mx / mLen

                      val muy = my / mLen

                      val movePath = buildRadialHoldDragPath(leftPadX, leftPadY, mux, muy, moveDragR, 5)

                      builder.addStroke(GestureDescription.StrokeDescription(movePath, 0L, 290L, false))

                      added = true

                  }



                  val aLen = hypot(ax, ay)

                  if (fire && aLen > 0.1f) {

                      val aux = ax / aLen

                      val auy = ay / aLen

                      // 15ms 시차로 우측 에임 패드를 당기고 바깥쪽에서 홀드한 뒤 205ms에 놓아 정확히 목표 각도로 발사!

                      val aimPath = buildRadialHoldDragPath(rightPadX, rightPadY, aux, auy, aimDragR, 3)

                      builder.addStroke(GestureDescription.StrokeDescription(aimPath, 15L, 190L, false))

                      added = true

                  }



                  if (!added) return



                  gestureBusy = true

                  lastGestureStartMs = System.currentTimeMillis()



                  val dispatched = dispatchGesture(

                      builder.build(),

                      object : GestureResultCallback() {

                          override fun onCompleted(gestureDescription: GestureDescription?) {

                              gestureBusy = false

                          }



                          override fun onCancelled(gestureDescription: GestureDescription?) {

                              gestureBusy = false

                          }

                      },

                      handler

                  )

                  if (!dispatched) {

                      gestureBusy = false

                  }

              }



              private fun scanMyHpAndAmmoFromBar(bmp: Bitmap, barCx: Float, barCy: Float, w: Int, h: Int) {

                  val barWidth = (w * 0.055f).toInt().coerceAtLeast(36)

                  val hpY = barCy.toInt().coerceIn(10, h - 25)

                  val ammoY = (barCy + h * 0.019f).toInt().coerceIn(10, h - 20)

                  val startX = (barCx - barWidth / 2f).toInt().coerceIn(10, w - barWidth - 10)



                  var hpColoredPixels = 0

                  var ammoOrangePixels = 0

                  val samples = 18

                  val dxStep = max(1, barWidth / samples)



                  for (i in 0 until samples) {

                      val x = startX + i * dxStep

                      val cHp = bmp.getPixel(x, hpY)

                      val hr = Color.red(cHp)

                      val hg = Color.green(cHp)

                      val hb = Color.blue(cHp)

                      if ((hg > 160 && hb < 115) || (hr > 180 && hg > 140 && hb < 95)) {

                          hpColoredPixels++

                      }



                      val cAm = bmp.getPixel(x, ammoY)

                      val ar = Color.red(cAm)

                      val ag = Color.green(cAm)

                      val ab = Color.blue(cAm)

                      if (ar > 195 && ag in 80..220 && bLow(ab)) {

                          ammoOrangePixels++

                      }

                  }



                  if (hpColoredPixels > 2) {

                      val measuredHp = (hpColoredPixels.toFloat() / samples.toFloat()).coerceIn(0.1f, 1.0f)

                      myHpRatio = myHpRatio * 0.7f + measuredHp * 0.3f

                  }

                  val measuredAmmo = ((ammoOrangePixels.toFloat() / samples.toFloat()) * 3.0f).coerceIn(0f, 3.0f)

                  myAmmoBars = myAmmoBars * 0.6f + (if (ammoOrangePixels == 0) 2.2f else measuredAmmo) * 0.4f

              }



              private fun bLow(b: Int): Boolean = b < 88



              private fun extractClusters(

                  mask: BooleanArray,

                  gridW: Int,

                  gridH: Int,

                  step: Int,

                  minCells: Int,

                  maxCells: Int

              ): List<ClusterBlob> {

                  val visited = BooleanArray(mask.size)

                  val blobs = mutableListOf<ClusterBlob>()

                  val queue = IntArray(256)



                  for (gy in 2 until gridH - 2) {

                      for (gx in 2 until gridW - 2) {

                          val startIdx = gy * gridW + gx

                          if (!mask[startIdx] || visited[startIdx]) continue



                          var sumX = 0f

                          var sumY = 0f

                          var minX = 99999f

                          var maxX = 0f

                          var minY = 99999f

                          var maxY = 0f

                          var count = 0

                          var qHead = 0

                          var qTail = 0

                          queue[qTail++] = startIdx

                          visited[startIdx] = true



                          while (qHead < qTail && count < maxCells + 15) {

                              val curr = queue[qHead++]

                              val cy = curr / gridW

                              val cx = curr % gridW

                              val px = cx * step.toFloat()

                              val py = cy * step.toFloat()

                              sumX += px

                              sumY += py

                              if (px < minX) minX = px

                              if (px > maxX) maxX = px

                              if (py < minY) minY = py

                              if (py > maxY) maxY = py

                              count++



                              for (ny in (cy - 2)..(cy + 2)) {

                                  for (nx in (cx - 2)..(cx + 2)) {

                                      if (nx in 1 until gridW - 1 && ny in 1 until gridH - 1) {

                                          val nIdx = ny * gridW + nx

                                          if (mask[nIdx] && !visited[nIdx] && qTail < queue.size) {

                                              visited[nIdx] = true

                                              queue[qTail++] = nIdx

                                          }

                                      }

                                  }

                              }

                          }



                          if (count in minCells..maxCells) {

                              blobs.add(ClusterBlob(sumX / count, sumY / count, count, minX, maxX, minY, maxY))

                          }

                      }

                  }

                  return blobs

              }



              private fun matchAndTrackProjectiles(

                  currBlobs: List<ClusterBlob>,

                  oldBlobs: List<ClusterBlob>,

                  dt: Float,

                  screenW: Int

              ): List<TrackedProjectile> {

                  val minProjectileSpeed = screenW * 0.20f

                  val maxJumpDist = screenW * 0.20f

                  val tracked = mutableListOf<TrackedProjectile>()



                  for (cb in currBlobs) {

                      var bestOld: ClusterBlob? = null

                      var bestDist = maxJumpDist

                      for (ob in oldBlobs) {

                          val d = hypot(cb.cx - ob.cx, cb.cy - ob.cy)

                          if (d < bestDist && d > 8f) {

                              bestDist = d

                              bestOld = ob

                          }

                      }

                      if (bestOld != null) {

                          val vx = (cb.cx - bestOld.cx) / dt

                          val vy = (cb.cy - bestOld.cy) / dt

                          val spd = hypot(vx, vy)

                          if (spd >= minProjectileSpeed) {

                              tracked.add(TrackedProjectile(cb.cx, cb.cy, vx, vy, spd))

                          }

                      }

                  }

                  return tracked

              }



              private fun computeRelativeTrajectoryDodge(

                  projectiles: List<TrackedProjectile>,

                  px: Float,

                  py: Float,

                  myVx: Float,

                  myVy: Float,

                  myMaxSpeed: Float,

                  screenW: Int

              ): Pair<Float, Float> {

                  if (projectiles.isEmpty()) return Pair(0f, 0f)

                  val safeRadius = screenW * 0.07f



                  val threats = projectiles.filter { p ->

                      val relVx = p.vx - myVx

                      val relVy = p.vy - myVy

                      val relSpd = hypot(relVx, relVy).coerceAtLeast(1f)

                      val rx = px - p.x

                      val ry = py - p.y

                      val along = (rx * relVx + ry * relVy) / relSpd

                      if (along <= -20f || along > screenW * 0.42f) {

                          false

                      } else {

                          val cpaX = p.x + (relVx / relSpd) * max(0f, along)

                          val cpaY = p.y + (relVy / relSpd) * max(0f, along)

                          hypot(px - cpaX, py - cpaY) < safeRadius

                      }

                  }



                  if (threats.isEmpty()) return Pair(0f, 0f)



                  var bestDirX = 0f

                  var bestDirY = 0f

                  var bestScore = -999999f



                  for (i in 0 until 8) {

                      val ang = (i * Math.PI / 4.0).toFloat()

                      val candUx = cos(ang)

                      val candUy = sin(ang)

                      val candVx = candUx * myMaxSpeed

                      val candVy = candUy * myMaxSpeed



                      var minClearance = 999999f

                      for (th in threats) {

                          val rvx = th.vx - candVx

                          val rvy = th.vy - candVy

                          val rSpd = hypot(rvx, rvy).coerceAtLeast(1f)

                          val rx = px - th.x

                          val ry = py - th.y

                          val along = ((rx * rvx + ry * rvy) / rSpd).coerceIn(0f, screenW * 0.4f)

                          val cpaX = th.x + (rvx / rSpd) * along

                          val cpaY = th.y + (rvy / rSpd) * along

                          val distAtCpa = hypot(px - cpaX, py - cpaY)

                          if (distAtCpa < minClearance) minClearance = distAtCpa

                      }



                      val userAlign = (candUx * cmdX + candUy * cmdY) * 28f

                      val totalScore = minClearance + userAlign

                      if (totalScore > bestScore) {

                          bestScore = totalScore

                          bestDirX = candUx

                          bestDirY = candUy

                      }

                  }



                  return Pair(bestDirX, bestDirY)

              }

          }

          ''')

          print("Android v3.1 source generated successfully.")

          EOF



      - name: Setup Gradle

        uses: gradle/actions/setup-gradle@v3

        with:

          gradle-version: '8.5'



      - name: Build Debug APK

        run: gradle assembleDebug



      - name: Rename APK for Easy Download

        run: cp app/build/outputs/apk/debug/app-debug.apk Brawl_AI_Overlay_v3.2.apk



      - name: Upload APK to Actions Tab

        uses: actions/upload-artifact@v4

        with:

          name: Brawl_AI_Overlay_v3.2_APK

          path: Brawl_AI_Overlay_v3.2.apk



      - name: Publish APK to GitHub Releases (Clean Old v1/v2 Releases)

        continue-on-error: true

        env:

          GH_TOKEN: ${{ secrets.GITHUB_TOKEN }}

        run: |

          gh release delete v1.0 --yes || true

          gh release delete v2.0 --yes || true

          gh release delete v3.0 --yes || true

          gh release delete v3.1 --yes || true

          gh release delete v3.2 --yes || true

          gh release create v3.2 Brawl_AI_Overlay_v3.2.apk --title "Brawl AI Overlay v3.2 최신 설치파일 (APK)" --notes "이전 v2.0 릴리즈 자동 삭제 + 직선 홀드-드래그(Radial Hold-Drag) 패드 엔진 + 발밑 원 평면 정렬 조준 수정판"

