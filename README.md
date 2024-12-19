# vtys
veri tabanındaki sql kodlarım


-- Temel tablo: Kisi (Ata Tablo)
CREATE TABLE "Kisi" (
    "kisi_id" SERIAL PRIMARY KEY,
    "ad" CHARACTER VARYING(40) NOT NULL,
    "soyad" CHARACTER VARYING(40) NOT NULL,
    "telefon" CHARACTER VARYING(15),
    "adres" CHARACTER VARYING(100)
);

-- Çocuk tablo: Musteri
CREATE TABLE "Musteri" (
    "kisi_id" INT PRIMARY KEY,
    "dogum_tarihi" DATE NOT NULL, -- Yeni alan
    "kimlik_no" CHARACTER VARYING(11) NOT NULL UNIQUE, -- Yeni alan
    CONSTRAINT "MusteriKisi" FOREIGN KEY ("kisi_id")
        REFERENCES "Kisi" ("kisi_id")
        ON DELETE CASCADE
        ON UPDATE CASCADE
);

-- Çocuk tablo: Personel
CREATE TABLE "Personel" (
    "kisi_id" INT PRIMARY KEY,
    "bayi_id" INT NOT NULL,
    "pozisyon" CHARACTER VARYING(50) NOT NULL, -- Yeni alan
    "maas" NUMERIC(10, 2) NOT NULL, -- Yeni alan
    CONSTRAINT "PersonelKisi" FOREIGN KEY ("kisi_id")
        REFERENCES "Kisi" ("kisi_id")
        ON DELETE CASCADE
        ON UPDATE CASCADE,
    CONSTRAINT "PersonelBayi" FOREIGN KEY ("bayi_id")
        REFERENCES "Bayi" ("bayi_id")
        ON DELETE CASCADE
        ON UPDATE CASCADE
);

-- Tablo: Sehir
CREATE TABLE "Sehir" (
    "sehir_id" SERIAL PRIMARY KEY,
    "sehir_adi" CHARACTER VARYING(40) NOT NULL,
    "il_plaka_kodu" CHARACTER VARYING(10) NOT NULL
);

-- Tablo: Bayi
CREATE TABLE "Bayi" (
    "bayi_id" SERIAL PRIMARY KEY,
    "sehir_id" INT NOT NULL,
    "bayi_adi" CHARACTER VARYING(50) NOT NULL,
    "telefon" CHARACTER VARYING(15),
    "adres" CHARACTER VARYING(100),
    CONSTRAINT "BayiSehir" FOREIGN KEY ("sehir_id")
        REFERENCES "Sehir" ("sehir_id")
        ON DELETE CASCADE
        ON UPDATE CASCADE
);

-- Önce yabancı anahtarlar olmadan tabloları oluştur
CREATE TABLE "Siparis" (
    "siparis_id" SERIAL PRIMARY KEY,
    "kisi_id" INT NOT NULL,
    "bayi_id" INT NOT NULL,
    "fatura_no" INT,
    "siparis_tarihi" DATE NOT NULL
);

CREATE TABLE "Fatura" (
    "fatura_no" SERIAL PRIMARY KEY,
    "siparis_id" INT NOT NULL,
    "fatura_tarihi" DATE NOT NULL
);

-- Daha sonra yabancı anahtarları ekleme işi
ALTER TABLE "Siparis"
ADD CONSTRAINT "SiparisFatura" FOREIGN KEY ("fatura_no")
REFERENCES "Fatura" ("fatura_no")
ON DELETE SET NULL
ON UPDATE CASCADE;

ALTER TABLE "Fatura"
ADD CONSTRAINT "FaturaSiparis" FOREIGN KEY ("siparis_id")
REFERENCES "Siparis" ("siparis_id")
ON DELETE CASCADE
ON UPDATE CASCADE;

-- Diğer yabancı anahtarları ekleme işi
ALTER TABLE "Siparis"
ADD CONSTRAINT "SiparisMusteri" FOREIGN KEY ("kisi_id")
REFERENCES "Musteri" ("kisi_id")
ON DELETE CASCADE
ON UPDATE CASCADE;

ALTER TABLE "Siparis"
ADD CONSTRAINT "SiparisBayi" FOREIGN KEY ("bayi_id")
REFERENCES "Bayi" ("bayi_id")
ON DELETE CASCADE
ON UPDATE CASCADE;



-- Tablo: Servis
CREATE TABLE "Servis" (
    "servis_id" SERIAL PRIMARY KEY,
    "bayi_id" INT NOT NULL,
    "servis_adi" CHARACTER VARYING(50) NOT NULL,
    CONSTRAINT "ServisBayi" FOREIGN KEY ("bayi_id")
        REFERENCES "Bayi" ("bayi_id")
        ON DELETE CASCADE
        ON UPDATE CASCADE
);

-- Tablo: Arac_Stok
CREATE TABLE "Arac_Stok" (
    "arac_stok_id" SERIAL PRIMARY KEY,
    "arac_id" INT NOT NULL,
    "bayi_id" INT NOT NULL,
    "miktar" INT NOT NULL,
    CONSTRAINT "AracStokArac" FOREIGN KEY ("arac_id")
        REFERENCES "Arac" ("arac_id")
        ON DELETE CASCADE
        ON UPDATE CASCADE,
    CONSTRAINT "AracStokBayi" FOREIGN KEY ("bayi_id")
        REFERENCES "Bayi" ("bayi_id")
        ON DELETE CASCADE
        ON UPDATE CASCADE
);

-- Tablo: Arac
CREATE TABLE "Arac" (
    "arac_id" SERIAL PRIMARY KEY,
    "tur_kodu" INT NOT NULL,
    "arac_model" INT NOT NULL,
    CONSTRAINT "AracTur" FOREIGN KEY ("tur_kodu")
        REFERENCES "Tur" ("tur_kodu")
        ON DELETE CASCADE
        ON UPDATE CASCADE,
    CONSTRAINT "AracModel" FOREIGN KEY ("arac_model")
        REFERENCES "Arac_Model" ("arac_model")
        ON DELETE CASCADE
        ON UPDATE CASCADE
);

-- Tablo: Tur
CREATE TABLE "Tur" (
    "tur_kodu" SERIAL PRIMARY KEY,
    "ad" CHARACTER VARYING(50) NOT NULL
);

-- Tablo: Arac_Model
CREATE TABLE "Arac_Model" (
    "arac_model" SERIAL PRIMARY KEY,
    "ad" CHARACTER VARYING(50) NOT NULL
);

-- Tablo: AracMalzeme
CREATE TABLE "AracMalzeme" (
    "arac_id" INT NOT NULL,
    "malzeme_id" INT NOT NULL,
    "miktar" INT NOT NULL,
    PRIMARY KEY ("arac_id", "malzeme_id"),
    CONSTRAINT "AracMalzemeArac" FOREIGN KEY ("arac_id")
        REFERENCES "Arac" ("arac_id")
        ON DELETE CASCADE
        ON UPDATE CASCADE,
    CONSTRAINT "AracMalzemeMalzeme" FOREIGN KEY ("malzeme_id")
        REFERENCES "Malzeme" ("malzeme_id")
        ON DELETE CASCADE
        ON UPDATE CASCADE
);

-- Tablo: Malzeme
CREATE TABLE "Malzeme" (
    "malzeme_id" SERIAL PRIMARY KEY,
    "tedarikci_id" INT NOT NULL,
    "adi" CHARACTER VARYING(50) NOT NULL,
    "stokMiktari" INT NOT NULL,
    CONSTRAINT "MalzemeTedarikci" FOREIGN KEY ("tedarikci_id")
        REFERENCES "Tedarikci" ("tedarikci_id")
        ON DELETE CASCADE
        ON UPDATE CASCADE
);

-- Tablo: Tedarikci
CREATE TABLE "Tedarikci" (
    "tedarikci_id" SERIAL PRIMARY KEY,
    "adi" CHARACTER VARYING(50) NOT NULL
);

-- tablolara veriler ekleyelim

-- Kisi tablosu
INSERT INTO "Kisi" ("kisi_id", "ad", "soyad", "telefon", "adres")
VALUES
(1, 'Ahmet', 'Yılmaz', '5551112233', 'Ankara, Türkiye'),
(2, 'Mehmet', 'Demir', '5554445566', 'İstanbul, Türkiye');

-- Musteri tablosu
INSERT INTO "Musteri" ("kisi_id", "dogum_tarihi", "kimlik_no")
VALUES
(1, '1990-01-01', '12345678901');

-- Personel tablosu
INSERT INTO "Personel" ("kisi_id", "bayi_id", "pozisyon", "maas")
VALUES
(2, 1, 'Satış Temsilcisi', 5000.00);

-- Sehir tablosu
INSERT INTO "Sehir" ("sehir_id", "sehir_adi", "il_plaka_kodu")
VALUES
(1, 'Ankara', '06'),
(2, 'İstanbul', '34');

-- Bayi tablosu
INSERT INTO "Bayi" ("bayi_id", "sehir_id", "bayi_adi", "telefon", "adres")
VALUES
(1, 1, 'Bayi Ankara', '3125551234', 'Ankara, Merkez'),
(2, 2, 'Bayi İstanbul', '2165555678', 'İstanbul, Kadıköy');

-- Siparis tablosu
INSERT INTO "Siparis" ("siparis_id", "kisi_id", "bayi_id", "fatura_no", "siparis_tarihi")
VALUES
(1, 1, 1, NULL, '2024-01-01');

-- Fatura tablosu
INSERT INTO "Fatura" ("fatura_no", "siparis_id", "fatura_tarihi")
VALUES
(1, 1, '2024-01-02');

-- Servis tablosu
INSERT INTO "Servis" ("servis_id", "bayi_id", "servis_adi")
VALUES
(1, 1, 'Ankara Teknik Servis');

-- Tur tablosu
INSERT INTO "Tur" ("tur_kodu", "ad")
VALUES
(1, 'SUV'),
(2, 'Sedan');

-- Arac_Model tablosu
INSERT INTO "Arac_Model" ("arac_model", "ad")
VALUES
(1, 'Model X'),
(2, 'Model Y');

-- Arac tablosu
INSERT INTO "Arac" ("arac_id", "tur_kodu", "arac_model")
VALUES
(1, 1, 1),
(2, 2, 2);

-- Arac_Stok tablosu
INSERT INTO "Arac_Stok" ("arac_stok_id", "arac_id", "bayi_id", "miktar")
VALUES
(1, 1, 1, 5),
(2, 2, 2, 10);

-- Malzeme tablosu
INSERT INTO "Malzeme" ("malzeme_id", "tedarikci_id", "adi", "stokMiktari")
VALUES
(1, 1, 'Lastik', 100),
(2, 1, 'Motor Yağı', 50);

-- Tedarikci tablosu
INSERT INTO "Tedarikci" ("tedarikci_id", "adi")
VALUES
(1, 'Tedarikçi AŞ');

-- AracMalzeme tablosu
INSERT INTO "AracMalzeme" ("arac_id", "malzeme_id", "miktar")
VALUES
(1, 1, 4),
(1, 2, 2);



--Tüm müşterileri listeleme işi
SELECT k."ad", k."soyad", m."dogum_tarihi", m."kimlik_no"
FROM "Musteri" m
INNER JOIN "Kisi" k ON m."kisi_id" = k."kisi_id";

--Siparişlere ait faturaları görmek:
SELECT s."siparis_id", s."siparis_tarihi", f."fatura_no", f."fatura_tarihi"
FROM "Siparis" s
INNER JOIN "Fatura" f ON s."siparis_id" = f."siparis_id";

--Bayilerdeki araç stoklarını görmek:
SELECT b."bayi_adi", a."arac_id", a."arac_model", s."miktar"
FROM "Bayi" b
INNER JOIN "Arac_Stok" s ON b."bayi_id" = s."bayi_id"
INNER JOIN "Arac" a ON s."arac_id" = a."arac_id";



---------------fonksiyonlar


--Araç Stok Güncellendiğinde Miktar Negatifse Durdurma
CREATE OR REPLACE FUNCTION kontrol_arac_stok()
RETURNS TRIGGER AS $$
BEGIN
    IF NEW.miktar < 0 THEN
        RAISE EXCEPTION 'Stok miktarı negatif olamaz!';
    END IF;
    RETURN NEW;
END;
$$ LANGUAGE plpgsql;

CREATE TRIGGER stok_guncelleme_trigger
BEFORE INSERT OR UPDATE ON "Arac_Stok"
FOR EACH ROW
EXECUTE FUNCTION kontrol_arac_stok();

--Fatura Silindiğinde Log Tutma
CREATE OR REPLACE FUNCTION log_fatura_silme()
RETURNS TRIGGER AS $$
BEGIN
    RAISE NOTICE 'Fatura silindi: %', OLD.fatura_no;
    RETURN OLD;
END;
$$ LANGUAGE plpgsql;

CREATE TRIGGER fatura_silme_trigger
AFTER DELETE ON "Fatura"
FOR EACH ROW
EXECUTE FUNCTION log_fatura_silme();

--Müşteri Doğum Tarihi Güncellendiğinde Log Tutma
CREATE OR REPLACE FUNCTION log_dogum_tarihi_guncelleme()
RETURNS TRIGGER AS $$
BEGIN
    RAISE NOTICE 'Müşteri doğum tarihi güncellendi: % -> %', OLD.dogum_tarihi, NEW.dogum_tarihi;
    RETURN NEW;
END;
$$ LANGUAGE plpgsql;

CREATE TRIGGER musteri_dogum_tarihi_guncelleme
AFTER UPDATE ON "Musteri"
FOR EACH ROW
EXECUTE FUNCTION log_dogum_tarihi_guncelleme();


---saklı yordamlar

--Yeni Bayi Ekleme
CREATE OR REPLACE PROCEDURE yeni_bayi_ekle(
	sehir_id INT,
    bayi_adi VARCHAR,
    telefon VARCHAR,
    adres VARCHAR
)
LANGUAGE plpgsql AS $$
BEGIN
    INSERT INTO "Bayi" ("sehir_id", "bayi_adi", "telefon", "adres")
    VALUES (sehir_id, bayi_adi, telefon, adres);
END;
$$;

--Müşteri Bilgisi Güncelleme
CREATE OR REPLACE PROCEDURE musteri_bilgi_guncelle(
    p_kisi_id INT,
    yeni_ad VARCHAR,
    yeni_soyad VARCHAR,
    yeni_telefon VARCHAR,
    yeni_adres VARCHAR
)
LANGUAGE plpgsql AS $$
BEGIN
    UPDATE "Kisi"
    SET "ad" = yeni_ad, "soyad" = yeni_soyad, "telefon" = yeni_telefon, "adres" = yeni_adres
    WHERE "kisi_id" = p_kisi_id;
END;
$$;


CREATE OR REPLACE PROCEDURE yeni_arac_ekle(
    p_tur_kodu INT,
    p_arac_model INT
)
LANGUAGE plpgsql AS $$
BEGIN
    INSERT INTO "Arac" ("tur_kodu", "arac_model")
    VALUES (p_tur_kodu, p_arac_model);
END;
$$;


CALL yeni_arac_ekle(3, 3);


--TÜM ARAÇLARI listeleme işi
SELECT 
    a."arac_id",
    t."ad" AS "tur_adi",
    m."ad" AS "model_adi"
FROM "Arac" a
INNER JOIN "Tur" t ON a."tur_kodu" = t."tur_kodu"
INNER JOIN "Arac_Model" m ON a."arac_model" = m."arac_model";


--Sipariş Silme Yordamı
CREATE OR REPLACE PROCEDURE siparis_sil(
    p_siparis_id INT
)
LANGUAGE plpgsql AS $$
BEGIN
    DELETE FROM "Fatura"
    WHERE "siparis_id" = p_siparis_id;

    DELETE FROM "Siparis"
    WHERE "siparis_id" = p_siparis_id;
END;
$$;

CALL siparis_sil(1);

-- denemeler

UPDATE "Arac_Stok"
SET "miktar" = -5
WHERE "arac_stok_id" = 1;

DELETE FROM "Fatura"
WHERE "fatura_no" = 1;

UPDATE "Musteri"
SET "dogum_tarihi" = '1992-02-02'
WHERE "kisi_id" = 1;

CALL yeni_bayi_ekle(1, 'Yeni Bayi', '3125557890', 'Çankaya, Ankara');

CALL musteri_bilgi_guncelle(1, 'Ahmet', 'Kaya', '5557776655', 'Bursa, Türkiye');


