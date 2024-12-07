<h1> Laporan Akhir Final Project OOP D </h1>

## 1. Informasi Umum

A. **Nama Game** : Weapmath - War of Math and Culture

B. **Anggota Kelompok** : 

  1. **Alvin Zanua Putra** - 5025231064
  2. **Adrian Aziz Santoso** - 5025221229
  3. **Muhammad Farrel Fathin Wibowo** - 5025231233 

C. **Tech Stack**: 

[Bahasa Pemrograman C#, Framework Unity API, Tools yang digunakan adalah Unity Editor, Visual Studio Code Editor, Assets Store Itch Io]

# 2. Deskripsi Game
## 2.1 Konsep Game

**Genre** : Educational Game Battle dengan tema Budaya Indonesia
**Gameplay/Rule**:

Game bertemakan perang adat yang menggabungkan unsur matematika dan budaya Indonesia. Pemain dapat memilih karakter dengan pakaian adat dari berbagai daerah. Setiap pertarungan melibatkan pemecahan soal matematika. Tingkat kesulitan soal disesuaikan dengan level kelas pemain (SMP).

- **Objective** : 

  Mengalahkan lawan dengan menjawab soal matematika dengan benar
  Mengumpulkan koin untuk membuka karakter dan rumah adat baru
  Mempelajari budaya Indonesia melalui visualisasi karakter dan bangunan adat hingga soal matematika

- **Single/Multi Player** : 

  - Single Player: Melawan AI komputer
  - Multi Player: Pertarungan 1 vs 1 secara online

## 2.2 Fitur Utama

1. **Fitur Single Player Mode** :  
   - Pemain dapat berlatih melawan komputer
   - Tingkat kesulitan soal matematika adaptif
   - Sistem reward berupa koin untuk setiap kemenangan

2. **Fitur Multi Player Mode** :
   - Sistem matchmaking online
   - Pertarungan realtime antar pemain
   - Host server untuk koneksi antar pemain

3. **Sistem Ekonomi & Progression** :

   - Sistem koin sebagai mata uang dalam game
   - Sistem unlock karakter dan rumah adat
   - Inapp purchase untuk mendapatkan koin tambahan
   - Achievement system

4. **Sistem Profil Pemain** :

   - Customizable username
   - Pemilihan gender
   - Pemilihan tingkat kelas
   - Unique ID generation dengan base64 encoding
   - Tracking statistik pemain

5. **Fitur Kustomisasi** :

   - Berbagai pilihan karakter dengan pakaian adat
   - Pilihan rumah adat dari berbagai daerah
   - Sistem unlock berbasis koin
   - Preview karakter dan rumah adat

# 3. Implementasi Fitur Wajib

## 3.1 Save/Load System

## A. **Implementasi** :

1. Fitur Untuk **menyimpan data** di bagian profile player berupa username, id, gender, tingkat kelas.
2. Fitur Untuk **menyimpan data karakter** yang dipilih oleh user saat ini.
3. Fitur Untuk **menyimpan rumah adat** yang dipilih oleh user saat ini.
4. Fitur Untuk **menyimpan koin atau achievement** yang didapatkan user setelah memenangkan game.
5. Fitur Untuk **menyimpan koin top up** yang dilakukan user melalui Google Play yaitu app in purchase.
6. Fitur Untuk **menyimpan audio settings** yang dipilih oleh user saat ini.

## B. **Konsep OOP**:

1. **Encapsulation**

        - Penggunaan access modifiers (public, private, protected)
        - Data hiding untuk variabel sensitif
        - Getter dan setter methods untuk kontrol akses

2. **Inheritance**

        - Base class untuk karakter
        - Child classes untuk variasi karakter
        - Inheritance dari MonoBehaviour

3. **Polymorphism**

        - Override methods untuk behavior spesifik
        - Interface implementation untuk system handlers

## C. **Penerapan SOLID**:

1. **Single Responsibility Principle**

        - Kelas SaveLoader fokus pada penyimpanan data
        - Kelas AudioSettings khusus menangani audio
        - Kelas UsernameChange spesifik untuk manajemen username

2. **Open/Closed Principle**

        - Sistem karakter yang extensible
        - Modular achievement system

3. **Interface Segregation**

        - Penggunaan interface IDetailedStoreListener
        - Pemisahan concern untuk network functionality


## D. **Code Snippet**:

### **1. SaveLoader.cs** :

```bash
using System.Collections;
using System.Collections.Generic;
using TMPro;
using UnityEngine;
using UnityEngine.UI;

public class SaveLoader : MonoBehaviour
{
    const string glyphs= "ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789!@#$%^&*()";

    [SerializeField] GameObject c1, c2, c3, c4, c5, c6, c7, c8, c9;
    [SerializeField] GameObject h1, h2, h3;
    [SerializeField] GameObject sc1, sc2, sc3, sc4, sc5, sc6, sc7, sc8, sc9;
    [SerializeField] GameObject sh1, sh2, sh3;
    [SerializeField] GameObject checkmark;
    [SerializeField] TextMeshProUGUI namesText;
    [SerializeField] TextMeshProUGUI gradeText;
    [SerializeField] TextMeshProUGUI idText;
    [SerializeField] TextMeshProUGUI genderText;
    [SerializeField] TextMeshProUGUI coinAmountText;
    [SerializeField] Button buyButton;
    [SerializeField] int c2price, c3price, c4price, c5price, c6price, c7price, c8price, c9price;
    [SerializeField] TextMeshProUGUI priceText;
    int charIndex = 0;
    int houseIndex = 0;
    bool isHouse = false;
    bool c2Own = false;
    bool c3Own = false;
    bool c4Own = false;
    bool c5Own = false;
    bool c6Own = false;
    bool c7Own = false;
    bool c8Own = false;
    bool c9Own = false;
    bool h2Own = false;
    bool h3Own = false;
    public bool proVersion = false;
    void Start()
    {
        charIndex = PlayerPrefs.GetInt("charIndex", 0);
        Debug.Log(charIndex);
        if (charIndex < 0 || charIndex > 8) {
            charIndex = 0;
        }
        houseIndex = PlayerPrefs.GetInt("houseIndex", 0);
        if (houseIndex < 0 || houseIndex > 2) {
            houseIndex = 0;
        }
        Debug.Log(charIndex);
        loadOwnedCharacters();
        applyChange();
        changeGrade(PlayerPrefs.GetInt("grade", 7));
        changeGender(PlayerPrefs.GetString("gender", "..."));
        idAssign();
        updateCoinAmount();
    }

    void Update(){
        if (isHouse){
            changeHouse();
        }
        else if (!isHouse) {
            changeChar();
        }
        selTextEnabler();
    }

    public void updateCoinAmount(){
        int coinOwned = PlayerPrefs.GetInt("coinOwned", 0);
        coinAmountText.text = coinOwned.ToString();
    }

    public void buyItem(){
        int coinOwned = PlayerPrefs.GetInt("coinOwned", 0);
        if (!isHouse){
            if (charIndex == 1){
                if (coinOwned >= c2price){
                    coinOwned = c2price;
                    PlayerPrefs.SetInt("isOwnC2", 1);
                    PlayerPrefs.SetInt("coinOwned", coinOwned);
                    updateCoinAmount();
                }
            }
            else if (charIndex == 2) {
                if (coinOwned >= c3price){
                    coinOwned = c3price;
                    PlayerPrefs.SetInt("isOwnC3", 1);
                    PlayerPrefs.SetInt("coinOwned", coinOwned);
                    updateCoinAmount();
                }
            }
            else if (charIndex == 3) {
                if (coinOwned >= c4price){
                    coinOwned = c4price;
                    PlayerPrefs.SetInt("isOwnC4", 1);
                    PlayerPrefs.SetInt("coinOwned", coinOwned);
                    updateCoinAmount();
                }
            }
            else if (charIndex == 4) {
                if (coinOwned >= c5price){
                    coinOwned = c5price;
                    PlayerPrefs.SetInt("isOwnC5", 1);
                    PlayerPrefs.SetInt("coinOwned", coinOwned);
                    updateCoinAmount();
                }
            }
            else if (charIndex == 5) {
                if (coinOwned >= c6price){
                    coinOwned = c6price;
                    PlayerPrefs.SetInt("isOwnC6", 1);
                    PlayerPrefs.SetInt("coinOwned", coinOwned);
                    updateCoinAmount();
                }
            }
            else if (charIndex == 6) {
                if (coinOwned >= c7price){
                    coinOwned = c7price;
                    PlayerPrefs.SetInt("isOwnC7", 1);
                    PlayerPrefs.SetInt("coinOwned", coinOwned);
                    updateCoinAmount();
                }
            }
            else if (charIndex == 7) {
                if (coinOwned >= c8price){
                    coinOwned = c8price;
                    PlayerPrefs.SetInt("isOwnC8", 1);
                    PlayerPrefs.SetInt("coinOwned", coinOwned);
                    updateCoinAmount();
                }
            }
            else if (charIndex == 8) {
                if (coinOwned >= c9price){
                    coinOwned = c9price;
                    PlayerPrefs.SetInt("isOwnC9", 1);
                    PlayerPrefs.SetInt("coinOwned", coinOwned);
                    updateCoinAmount();
                }
            }
        }
        else if (isHouse) {
            if (houseIndex == 1) {
                if (coinOwned >= 250){
                    coinOwned = 250;
                    PlayerPrefs.SetInt("isOwnH2", 1);
                    PlayerPrefs.SetInt("coinOwned", coinOwned);
                    updateCoinAmount();
                }
            }
            else if (houseIndex == 2) {
                if (coinOwned >= 250){
                    coinOwned = 250;
                    PlayerPrefs.SetInt("isOwnH3", 1);
                    PlayerPrefs.SetInt("coinOwned", coinOwned);
                    updateCoinAmount();
                }
            }
        }
        loadOwnedCharacters();
    }

    private void loadOwnedCharacters() {
        int isOwnC2 = PlayerPrefs.GetInt("isOwnC2", 0);
        int isOwnC3 = PlayerPrefs.GetInt("isOwnC3", 0);
        int isOwnC4 = PlayerPrefs.GetInt("isOwnC4", 0);
        int isOwnC5 = PlayerPrefs.GetInt("isOwnC5", 0);
        int isOwnC6 = PlayerPrefs.GetInt("isOwnC6", 0);
        int isOwnC7 = PlayerPrefs.GetInt("isOwnC7", 0);
        int isOwnC8 = PlayerPrefs.GetInt("isOwnC8", 0);
        int isOwnC9 = PlayerPrefs.GetInt("isOwnC9", 0);
        if (isOwnC2 == 1 || proVersion) c2Own = true;
        if (isOwnC3 == 1 || proVersion) c3Own = true;
        if (isOwnC4 == 1 || proVersion) c4Own = true;
        if (isOwnC5 == 1 || proVersion) c5Own = true;
        if (isOwnC6 == 1 || proVersion) c6Own = true;
        if (isOwnC7 == 1 || proVersion) c7Own = true;
        if (isOwnC8 == 1 || proVersion) c8Own = true;
        if (isOwnC9 == 1 || proVersion) c9Own = true;

        int isOwnH2 = PlayerPrefs.GetInt("isOwnH2", 0);
        int isOwnH3 = PlayerPrefs.GetInt("isOwnH3", 0);
        if (isOwnH2 == 1 || proVersion) h2Own = true;
        if (isOwnH3 == 1 || proVersion) h3Own = true;
    }

    private void idAssign(){
        string idGet = PlayerPrefs.GetString("id", "0");
        if (idGet == "0"){
            setID();
        }
        idText.text = "ID: " + PlayerPrefs.GetString("id");
    }

    private void setID(){
        string myString = "";
        for(int i = 0; i < 12; i++){
            myString += glyphs[Random.Range(0, glyphs.Length)];
        }
        PlayerPrefs.SetString("id", myString);
    }

    public void changeGrade(int gr){
        PlayerPrefs.SetInt("grade", gr);
        gradeText.text = "Kelas " + gr;
    }

    public void changeGender(string gender){
        PlayerPrefs.SetString("gender", gender);
        genderText.text = gender;
    }

    public void next() {
        if(!isHouse){
            charIndex = charIndex + 1;
            charIndex = charIndex % 9;
        }
        else if(isHouse){
            houseIndex = houseIndex + 1;
            houseIndex = houseIndex % 3;
        }
    }

    public void prev() {
        if(!isHouse){
            charIndex = charIndex  1;
            if (charIndex < 0) {
                charIndex = 8;
            }
        }
        else if (isHouse){
            houseIndex = houseIndex  1;
            if (houseIndex < 0) {
                houseIndex = 2;
            }
        }
    }

    public void changeTab(){
        isHouse = !isHouse;
    }

    public void applyChange(){
        print(charIndex);
        switch (charIndex) {
            case 0:
                disableChar();
                c1.SetActive(true);
                PlayerPrefs.SetInt("charIndex", charIndex);
                break;
            case 1:
                if (c2Own)  {
                    disableChar();
                    c2.SetActive(true);
                    PlayerPrefs.SetInt("charIndex", charIndex);
                }
                break;
            case 2:
                if (c3Own){
                    disableChar();
                    c3.SetActive(true);
                    PlayerPrefs.SetInt("charIndex", charIndex);
                }
                break;
            case 3:
                if (c4Own){
                    disableChar();
                    c4.SetActive(true);
                    PlayerPrefs.SetInt("charIndex", charIndex);
                }
                break;
            case 4:
                if (c5Own){
                    disableChar();
                    c5.SetActive(true);
                    PlayerPrefs.SetInt("charIndex", charIndex);
                }
                break;
            case 5:
                if (c6Own){
                    disableChar();
                    c6.SetActive(true);
                    PlayerPrefs.SetInt("charIndex", charIndex);
                }
                break;
            case 6:
                if (c7Own){
                    disableChar();
                    c7.SetActive(true);
                    PlayerPrefs.SetInt("charIndex", charIndex);
                }
                break;
            case 7:
                if (c8Own){
                    disableChar();
                    c8.SetActive(true);
                    PlayerPrefs.SetInt("charIndex", charIndex);
                }
                break;
            case 8:
                if (c9Own){
                    disableChar();
                    c9.SetActive(true);
                    PlayerPrefs.SetInt("charIndex", charIndex);
                }
                break;
            default:
                break;
        }
        switch (houseIndex) {
            case 0:
                disableHouse();
                h1.SetActive(true);
                PlayerPrefs.SetInt("houseIndex", houseIndex);
                break;
            case 1:
                if (h2Own) {
                    disableHouse();
                    h2.SetActive(true);
                    PlayerPrefs.SetInt("houseIndex", houseIndex);
                }
                break;
            case 2:
                if (h3Own) {
                    disableHouse();
                    h3.SetActive(true);
                    PlayerPrefs.SetInt("houseIndex", houseIndex);
                }
                break;
            default:
                break;
        }
    }


    private void disableHouse() {
        h1.SetActive(false);
        h2.SetActive(false);
        h3.SetActive(false);
    }
    private void disableChar() {
        c1.SetActive(false);
        c2.SetActive(false);
        c3.SetActive(false);
        c4.SetActive(false);
        c5.SetActive(false);
        c6.SetActive(false);
        c7.SetActive(false);
        c8.SetActive(false);
        c9.SetActive(false);
    }

    private void selTextEnabler(){
        if (isHouse){
            if (houseIndex == PlayerPrefs.GetInt("houseIndex")){
                checkmark.SetActive(true);
            }
            else if (houseIndex != PlayerPrefs.GetInt("houseIndex")){
                checkmark.SetActive(false);
            }
        }
        if (!isHouse){
            if (charIndex == PlayerPrefs.GetInt("charIndex")){
                checkmark.SetActive(true);
            }
            else if (charIndex != PlayerPrefs.GetInt("charIndex")){
                checkmark.SetActive(false);
            }
        }
    }

    private void changeHouse() {
        priceText.text = "250";
        switch (houseIndex) {
            case 0:
                namesText.text = "kalimantan";
                sh1.SetActive(true);
                sh2.SetActive(false);
                sh3.SetActive(false);
                buyButton.gameObject.SetActive(false);
                break;
            case 1:
                namesText.text = "sulawesi";
                sh1.SetActive(false);
                sh2.SetActive(true);
                sh3.SetActive(false);

                if (!h2Own){
                    sh2.GetComponent<SpriteRenderer>().color = Color.black;
                    buyButton.gameObject.SetActive(true);
                }
                else if (h2Own){
                    sh2.GetComponent<SpriteRenderer>().color = Color.white;
                    buyButton.gameObject.SetActive(false);
                }
                break;
            case 2:
                namesText.text = "jawa";
                sh1.SetActive(false);
                sh2.SetActive(false);
                sh3.SetActive(true);
                if (!h3Own){
                    sh3.GetComponent<SpriteRenderer>().color = Color.black;
                    buyButton.gameObject.SetActive(true);
                }
                else if (h3Own){
                    sh3.GetComponent<SpriteRenderer>().color = Color.white;
                    buyButton.gameObject.SetActive(false);
                }
                break;
            default:
                break;
        }
    }

    private void changeChar() {
        switch (charIndex) {
            case 0:
                namesText.text = "jawa barat";
                sc1.SetActive(true);
                sc2.SetActive(false);
                sc3.SetActive(false);
                sc4.SetActive(false);
                sc5.SetActive(false);
                sc6.SetActive(false);
                sc7.SetActive(false);
                sc8.SetActive(false);
                sc9.SetActive(false);
                buyButton.gameObject.SetActive(false);
                break;
            case 1:
                namesText.text = "sumatera barat";
                sc1.SetActive(false);
                sc2.SetActive(true);
                sc3.SetActive(false);
                sc4.SetActive(false);
                sc5.SetActive(false);
                sc6.SetActive(false);
                sc7.SetActive(false);
                sc8.SetActive(false);
                sc9.SetActive(false);
                priceText.text = c2price.ToString();
                if (!c2Own){
                    sc2.GetComponent<SpriteRenderer>().color = Color.black;
                    buyButton.gameObject.SetActive(true);
                }
                else if (c2Own){
                    sc2.GetComponent<SpriteRenderer>().color = Color.white;
                    buyButton.gameObject.SetActive(false);
                }
                break;
            case 2:
                namesText.text = "sumatera utara";
                sc1.SetActive(false);
                sc2.SetActive(false);
                sc3.SetActive(true);
                sc4.SetActive(false);
                sc5.SetActive(false);
                sc6.SetActive(false);
                sc7.SetActive(false);
                sc8.SetActive(false);
                sc9.SetActive(false);
                priceText.text = c3price.ToString();
                if (!c3Own){
                    sc3.GetComponent<SpriteRenderer>().color = Color.black;
                    buyButton.gameObject.SetActive(true);
                }
                else if (c3Own){
                    sc3.GetComponent<SpriteRenderer>().color = Color.white;
                    buyButton.gameObject.SetActive(false);
                }
                break;
            case 3:
                namesText.text = "palembang (L)";
                sc1.SetActive(false);
                sc2.SetActive(false);
                sc3.SetActive(false);
                sc4.SetActive(true);
                sc5.SetActive(false);
                sc6.SetActive(false);
                sc7.SetActive(false);
                sc8.SetActive(false);
                sc9.SetActive(false);
                priceText.text = c4price.ToString();
                if (!c4Own){
                    sc4.GetComponent<SpriteRenderer>().color = Color.black;
                    buyButton.gameObject.SetActive(true);
                }
                else if (c4Own){
                    sc4.GetComponent<SpriteRenderer>().color = Color.white;
                    buyButton.gameObject.SetActive(false);
                }
                break;
            case 4:
                namesText.text = "palembang (P)";
                sc1.SetActive(false);
                sc2.SetActive(false);
                sc3.SetActive(false);
                sc4.SetActive(false);
                sc5.SetActive(true);
                sc6.SetActive(false);
                sc7.SetActive(false);
                sc8.SetActive(false);
                sc9.SetActive(false);
                priceText.text = c5price.ToString();
                if (!c5Own){
                    sc5.GetComponent<SpriteRenderer>().color = Color.black;
                    buyButton.gameObject.SetActive(true);
                }
                else if (c5Own){
                    sc5.GetComponent<SpriteRenderer>().color = Color.white;
                    buyButton.gameObject.SetActive(false);
                }
                break;
            case 5:
                namesText.text = "betawi (L)";
                sc1.SetActive(false);
                sc2.SetActive(false);
                sc3.SetActive(false);
                sc4.SetActive(false);
                sc5.SetActive(false);
                sc6.SetActive(true);
                sc7.SetActive(false);
                sc8.SetActive(false);
                sc9.SetActive(false);
                priceText.text = c6price.ToString();
                if (!c6Own){
                    sc6.GetComponent<SpriteRenderer>().color = Color.black;
                    buyButton.gameObject.SetActive(true);
                }
                else if (c6Own){
                    sc6.GetComponent<SpriteRenderer>().color = Color.white;
                    buyButton.gameObject.SetActive(false);
                }
                break;
            case 6:
                namesText.text = "betawi (P)";
                sc1.SetActive(false);
                sc2.SetActive(false);
                sc3.SetActive(false);
                sc4.SetActive(false);
                sc5.SetActive(false);
                sc6.SetActive(false);
                sc7.SetActive(true);
                sc8.SetActive(false);
                sc9.SetActive(false);
                priceText.text = c7price.ToString();
                if (!c7Own){
                    sc7.GetComponent<SpriteRenderer>().color = Color.black;
                    buyButton.gameObject.SetActive(true);
                }
                else if (c7Own){
                    sc7.GetComponent<SpriteRenderer>().color = Color.white;
                    buyButton.gameObject.SetActive(false);
                }
                break;
            case 7:
                namesText.text = "sunda (L)";
                sc1.SetActive(false);
                sc2.SetActive(false);
                sc3.SetActive(false);
                sc4.SetActive(false);
                sc5.SetActive(false);
                sc6.SetActive(false);
                sc7.SetActive(false);
                sc8.SetActive(true);
                sc9.SetActive(false);
                priceText.text = c8price.ToString();
                if (!c8Own){
                    sc8.GetComponent<SpriteRenderer>().color = Color.black;
                    buyButton.gameObject.SetActive(true);
                }
                else if (c8Own){
                    sc8.GetComponent<SpriteRenderer>().color = Color.white;
                    buyButton.gameObject.SetActive(false);
                }
                break;
            case 8:
                namesText.text = "sunda (P)";
                sc1.SetActive(false);
                sc2.SetActive(false);
                sc3.SetActive(false);
                sc4.SetActive(false);
                sc5.SetActive(false);
                sc6.SetActive(false);
                sc7.SetActive(false);
                sc8.SetActive(false);
                sc9.SetActive(true);
                priceText.text = c9price.ToString();
                if (!c9Own){
                    sc9.GetComponent<SpriteRenderer>().color = Color.black;
                    buyButton.gameObject.SetActive(true);
                }
                else if (c9Own){
                    sc9.GetComponent<SpriteRenderer>().color = Color.white;
                    buyButton.gameObject.SetActive(false);
                }
                break;
            default:
                break;
        }
    }
}
```

## **2. UsernameChange.cs** :

```bash
using System.Collections;
using System.Collections.Generic;
using TMPro;
using UnityEngine;
using UnityEngine.UI;

public class UsernameChange : MonoBehaviour
{
    [SerializeField] TMP_InputField usernameInput;
    [SerializeField] TextMeshProUGUI username;
    [SerializeField] TextMeshProUGUI usernameProfile;

    private void Start() {
        string savedUsername = PlayerPrefs.GetString("username");
        if (savedUsername != "") {
            username.text = savedUsername;
            usernameProfile.text = savedUsername;
        }
    }

    public void changeUsername() {
        if (usernameInput != null) {
            PlayerPrefs.SetString("username", usernameInput.text);
            username.text = usernameInput.text;
            usernameProfile.text = usernameInput.text;
        }
    }
}
```

## **3. AudioSettings.cs** :

```bash
using System.Collections;
using System.Collections.Generic;
using UnityEngine;
using UnityEngine.UI;

public class AudioSettings : MonoBehaviour
{
    public bool musicEnabled;
    public bool sfxEnabled;
    [SerializeField] AudioSource msc, sfx;
    [SerializeField] Toggle mscT, sfxT;

    // Start is called before the first frame update
    void Start()
    {
        int mscE = PlayerPrefs.GetInt("msc", 1);
        int sfxE = PlayerPrefs.GetInt("sfx", 1);
        if (mscE == 1){
            musicEnabled = true;
            mscT.isOn = true;
        }
        else if (mscE == 0){
            musicEnabled = false;
            mscT.isOn = false;
        }
        if (sfxE == 1){
            sfxEnabled = true;
            sfxT.isOn = true;
        }
        else if (sfxE == 0){
            sfxEnabled = false;
            sfxT.isOn = false;
        }
    }

    // Update is called once per frame
    void Update()
    {

        if (musicEnabled){
            msc.enabled = true;
        }
        else if (!musicEnabled){
            msc.enabled = false;
        }

        if (sfxEnabled){
            sfx.enabled = true;
        }
        else if (!sfxEnabled){
            sfx.enabled = false;
        }
    }

    public void changeMsc(bool enabler){
        musicEnabled = enabler;
        if (musicEnabled) {
            PlayerPrefs.SetInt("msc", 1);
        }
        else if (!musicEnabled) {
            PlayerPrefs.SetInt("msc", 0);
        }
    }

    public void changeSfx(bool enabler){
        sfxEnabled = enabler;
        if (sfxEnabled) {
            PlayerPrefs.SetInt("sfx", 1);
        }
        else if (!sfxEnabled) {
            PlayerPrefs.SetInt("sfx", 0);
        }
    }
}
```

## 4. **Player Controller.cs** :

```bash
using System.Collections;
using System.Collections.Generic;
using Unity.Netcode;
using UnityEngine;

public class PlayerChanger : NetworkBehaviour
{
    GameManager gm;

    void Start()
    {
        gm = (GameManager)FindObjectOfType(typeof(GameManager));
        if (IsServer)
        {
            gm.changePlayer(1);
            gm.startMatch();
        }
        else if (IsClient)
        {
            gm.changePlayer(2);
        }
        gm.waitingPlayer();
    }
}
```

## 5. **ShopScripts.cs** :

```bash
using System.Collections;
using System.Collections.Generic;
using UnityEngine;
using System;
using UnityEngine.Purchasing;
using UnityEngine.Purchasing.Extension;

[Serializable]
public class ConsumableItem {
    public string Name;
    public string Id;
    public string desc;
    public float price;
}

public class ShopScript : MonoBehaviour, IDetailedStoreListener
{
    IStoreController isc;

    [SerializeField] SaveLoader sl;
    public ConsumableItem c250, c500, c1500;

    void Start(){
        SetupBuilder();
    }

    private void SetupBuilder(){
        var builder = ConfigurationBuilder.Instance(StandardPurchasingModule.Instance());

        builder.AddProduct(c250.Id, ProductType.Consumable);
        builder.AddProduct(c500.Id, ProductType.Consumable);
        builder.AddProduct(c1500.Id, ProductType.Consumable);

        UnityPurchasing.Initialize(this, builder);
    }

    public void OnInitialized(IStoreController controller, IExtensionProvider extensions)
    {
        print("Initialized");
        isc = controller;
    }

    public void buy10button(){
        isc.InitiatePurchase(c250.Id);
    }

    public void buy100button(){
        isc.InitiatePurchase(c500.Id);
    }

    public void buy500button(){
        isc.InitiatePurchase(c1500.Id);
    }

    public PurchaseProcessingResult ProcessPurchase(PurchaseEventArgs purchaseEvent)
    {
        var product = purchaseEvent.purchasedProduct;

        print("Purchased: " + product.definition.id);

        if (product.definition.id == c250.Id) {
            _buyCoin250();
        }
        else if (product.definition.id == c500.Id) {
            _buyCoin500();
        }
        else if (product.definition.id == c1500.Id) {
            _buyCoin1500();
        }

        return PurchaseProcessingResult.Complete;
    }

    private void _buyCoin250(){
        int coinOwned = PlayerPrefs.GetInt("coinOwned", 0);
        coinOwned += 250;
        PlayerPrefs.SetInt("coinOwned", coinOwned);
        sl.updateCoinAmount();
    }

    private void _buyCoin500(){
        int coinOwned = PlayerPrefs.GetInt("coinOwned", 0);
        coinOwned += 500;
        PlayerPrefs.SetInt("coinOwned", coinOwned);
        sl.updateCoinAmount();
    }

    private void _buyCoin1500(){
        int coinOwned = PlayerPrefs.GetInt("coinOwned", 0);
        coinOwned += 1500;
        PlayerPrefs.SetInt("coinOwned", coinOwned);
        sl.updateCoinAmount();
    }


    public void OnInitializeFailed(InitializationFailureReason error)
    {
        print("initialization failure: " + error);
    }

    public void OnInitializeFailed(InitializationFailureReason error, string message)
    {
        print("initialization failure: " + error + " |||| " + message);
    }

    public void OnPurchaseFailed(Product product, PurchaseFailureReason failureReason)
    {
        print("purchase failed " + product.definition.id + " |||| " + failureReason);
    }


    public void OnPurchaseFailed(Product product, PurchaseFailureDescription failureDescription)
    {
        print("purchase failed " + product.definition.id + " |||| " + failureDescription);
    }
}
```

### 3.2 Achievement System

**A. Jenis Achievement**:

    1. Implementasi State Management ialah berupa save data Current character selection, Active achievements, Coin balanced, dan Match history tanpa UI.

    2. Error Handling yang diimplementasikan ialah ketika pada mode multiplayer saat user jaringan lag di game ini terdapat bug jika salah 1 player menang dan player ke 2 lag cara menangani nya ketika user menyerang kita tambahkan Kalau sudah 1 detik langsung balikan variable ke 0 Jadi nggak terhitung kena hit pada proses penyerangan sehingga antara player yang kalah dan menang itu tetap matching bahwa yang menang mendapatkan koin 1 biji dan yang kalah 0.

**B. Konsep OOP**:

    1. Encapsulation: Data seperti koin, karakter, dan hasil pertandingan disimpan dalam SaveLoader dan hanya bisa diakses melalui metode khusus.
    2. Inheritance: Kelas SaveLoader bisa diperluas untuk menangani data tambahan.
    3. Polymorphism: Metode serangan bisa fleksibel sesuai kondisi pemain.
    4. Abstraction: Menyembunyikan detail implementasi sehingga mudah dipahami.

**C. Penerapan SOLID**:

    1. **SRP** : SaveLoader hanya menangani penyimpanan data, tidak ada campur aduk.
    2. **OCP** : Bisa ditambah fungsinya tanpa ubah yang lama.
    3. **LSP** : Kelas turunan bisa menggantikan SaveLoader tanpa masalah.
    4. **ISP** : Bisa buat antarmuka terpisah seperti ICoinManager.
    5. **DIP** : Dengan antarmuka, penyimpanan bisa diganti tanpa ubah kode utama.


**D. Code Snippet**:

### +>> State Management :

## **1. SaveLoader.cs** :

```bash
using TMPro;
using UnityEngine;

public class SaveLoader : MonoBehaviour
{
    [SerializeField] private TextMeshProUGUI coinAmountText;

    public void UpdateCoinAmount()
    {
        int coinOwned = Mathf.Max(PlayerPrefs.GetInt("coinOwned", 0), 0);
        coinAmountText.text = coinOwned.ToString();
    }

    public void AddCoins(int amount)
    {
        int currentCoins = PlayerPrefs.GetInt("coinOwned", 0);
        int newTotal = Mathf.Max(currentCoins + amount, 0);
        PlayerPrefs.SetInt("coinOwned", newTotal);
        UpdateCoinAmount();
    }

    public void SaveCharacterSelection(int characterId)
    {
        PlayerPrefs.SetInt("selectedCharacter", characterId);
    }

    public int LoadCharacterSelection()
    {
        return PlayerPrefs.GetInt("selectedCharacter", 0);
    }

    public void SaveMatchHistory(int matchResult)
    {
        PlayerPrefs.SetInt("lastMatchResult", matchResult);
    }
}
```

### +> 2. Error Handling :

## **2. Error Handling.cs** :

```bash
# untuk ini tidak ada tampilan UI nya hanya sebagai conter error saja
private bool isPlayerLagging = false;
private float lagThreshold = 1.0f; // waktu dalam detik

public void HandleAttack()
{
    if (!isPlayerLagging)
    {
        // Melakukan serangan
        PerformAttack();
    }
    else
    {
        Debug.Log("Player sedang lag, serangan tidak dihitung.");
        ResetAttack();
    }
}

private void PerformAttack()
{
    // Eksekusi serangan jika pemain tidak lag
    if (Time.time  lastAttackTime > lagThreshold)
    {
        lastAttackTime = Time.time;
        // Proses serangan berhasil
        UpdateGameStatus();
    }
}

private void ResetAttack()
{
    // Mengatur ulang variabel yang terkait serangan jika lag
    isPlayerLagging = false;
}
```

## 4. Implementasi Fitur Lain

### 4.1 Fitur Loading

- **Implementasi**:
- **Konsep OOP**:
- **Penerapan SOLID (Optional)**:
- **Design Pattern yang Digunakan (Optional)**:
- **Code Snippet**:

```
[Code snippet here]
```

### 4.2 Fitur 2

- **Implementasi**:
- **Konsep OOP**:
- **Penerapan SOLID (Optional)**:
- **Design Pattern yang Digunakan (Optional)**:
- **Code Snippet**:

```
[Code snippet here]
```

## 5. Screenshot dan Demo


![images](/assets/images/7.jpg)

![images](/assets/images/1.jpg)

![images](/assets/images/3.jpg)

![images](/assets/images/4.jpg)

![images](/assets/images/2.jpg)

![images](/assets/images/8.jpg)

![images](/assets/images/10.jpg)

![images](/assets/images/5.jpg)

![images](/assets/images/11.jpg)

![images](/assets/images/12.jpg)

![images](/assets/images/13.jpg)

![images](/assets/images/6.jpg)

![images](/assets/images/14.jpg)



- **Link Demo Video**: 
   ### [Demo Youtube : Klik Disini !](https://www.youtube.com/watch?v=J1j7jJt1J8Q)

## 6. Panduan Instalasi dan Menjalankan Game

### A. **Opsi 1** : "Instalasi dan Menjalankan Game di Localhost (PC/Laptop) dengan Unity Editor"

1. **Persyaratan**:
   - Unity Hub
   - Unity Editor
   - Text Editor (VS Code, Sublime, dll)
   - Git
2. **Langkah**:
    - Clone repository ini
        ```bash
        
        ```
    - Buka Unity Hub

### B. **Opsi 2** : "Dapat dijalankan di Android " 



## 7. Kendala dan Solusi

1. **Kendala 1**:
   - Solusi:
2. **Kendala 2**:
   - Solusi:

## 8. Kesimpulan dan Pembelajaran

- **Kesimpulan**:
- **Pembelajaran**:
