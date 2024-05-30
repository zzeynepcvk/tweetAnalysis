# Proje: Türkçe Tweet Analizi ve Küfür Tespiti

Bu proje, Türkçe tweetlerde küfürlü ve hakaret içeren ifadelerin tespit edilmesi ve analiz edilmesi amacıyla geliştirilmiştir. Projede kullanılan adımlar ve araçlar aşağıda detaylı olarak açıklanmıştır.

## İçindekiler
1. [Kurulum](#kurulum)
2. [Veri Yükleme](#veri-yukleme)
3. [Veri Ön İşleme](#veri-on-isleme)
4. [Küfür Analizi](#kufur-analizi)
5. [Grafiksel Analiz](#grafiksel-analiz)
6. [Makine Öğrenmesi Modeli](#makine-ogrenmesi-modeli)
7. [Sonuçlar](#sonuclar)
8. [Dosyalar](#dosyalar)

## Kurulum <a name="kurulum"></a>

Projeyi çalıştırmak için aşağıdaki Python kütüphanelerine ihtiyaç vardır:

```bash
pip install pandas
pip install zemberek-python
pip install numpy
pip install matplotlib
pip install networkx
pip install plotly
pip install scikit-learn
```

## Veri Yükleme <a name="veri-yukleme"></a>

Tweet verilerini yüklemek için `pandas` kütüphanesi kullanılmıştır:

```python
import pandas as pd

df = pd.read_csv('newcsv.csv', sep=',', encoding='ISO-8859-9')
```

## Veri Ön İşleme <a name="veri-on-isleme"></a>

Veri ön işleme aşamaları şunlardır:
1. Metinlerin küçük harfe dönüştürülmesi.
2. Noktalama işaretlerinin ve rakamların kaldırılması.
3. Türkçe stop words (sık kullanılan ama anlam taşımayan kelimeler) kelimelerin temizlenmesi.

```python
import re

def normalize_text(text):
    text = text.lower()
    text = re.sub(r'[^\w\s]', '', text)
    text = re.sub(r'\d+', '', text)
    text = re.sub(r'\s+', ' ', text).strip()
    return text

turkish_stop_words = ['acaba', 'ama', 'aslında', 'az', 'bazı', 'belki', 'biri', 'birkaç', 'birşey', 'biz', 'bu', 'çok', 'çünkü', 'da', 'daha', 'de', 'defa', 'diye', 'eğer', 'en', 'gibi', 'hem', 'hep', 'hepsi', 'her', 'hiç', 'için', 'ile', 'ise', 'kez', 'ki', 'kim', 'mı', 'mu', 'mü', 'nasıl', 'ne', 'neden', 'nerde', 'nerede', 'nereye', 'niçin', 'niye', 'o', 'sanki', 'şey', 'siz', 'şu', 'tüm', 've', 'veya', 'ya', 'yani']

def remove_turkish_stop_words(text):
    words = text.split()
    filtered_words = [word for word in words if word.lower() not in turkish_stop_words]
    filtered_text = ' '.join(filtered_words)
    return filtered_text

df['Paylaşım'] = df['Paylaşım'].apply(normalize_text)
df['Paylaşım'] = df['Paylaşım'].apply(remove_turkish_stop_words)
```

## Küfür Analizi <a name="kufur-analizi"></a>

Karaliste kelimeleri içeren bir dosya (`karaliste.txt`) kullanarak tweetlerdeki küfürlü kelimelerin sayısı hesaplanmıştır:

```python
from collections import defaultdict

with open('karaliste.txt', 'r', encoding='utf-8') as f:
    karaliste = set(f.read().split())

karaliste_counts = defaultdict(int)

for tweet in df['Paylaşım']:
    words = tweet.split()
    for word in words:
        if word in karaliste:
            karaliste_counts[word] += 1

karaliste_df = pd.DataFrame(karaliste_counts.items(), columns=['Kelime', 'Kullanım Sayısı'])
karaliste_df.to_csv('karaliste_kullanim_sayilari.csv', index=False)
```

## Grafiksel Analiz <a name="grafiksel-analiz"></a>

Küfürlü kelimeler arasındaki ilişkiler grafiksel olarak analiz edilmiştir:

```python
import networkx as nx
import matplotlib.pyplot as plt
import random

G = nx.Graph()

for index, row in karaliste_df.iterrows():
    word = row['Kelime']
    G.add_node(word, weight=row['Kullanım Sayısı'])

for index, row in karaliste_df.iterrows():
    word1 = row['Kelime']
    for index2, row2 in karaliste_df.iterrows():
        word2 = row2['Kelime']
        if word1 != word2:
            weight = min(row['Kullanım Sayısı'], row2['Kullanım Sayısı'])
            G.add_edge(word1, word2, weight=weight)

num_nodes_to_keep = 30
random_nodes = random.sample(list(G.nodes()), num_nodes_to_keep)
subgraph = G.subgraph(random_nodes)

node_sizes = [d * 100 for d in nx.get_node_attributes(subgraph, 'weight').values()]

plt.figure(figsize=(50, 50))
pos = nx.spring_layout(subgraph, seed=42, k=3.0)
nx.draw(subgraph, pos, node_size=max(node_sizes), node_color='lightblue', font_size=10, with_labels=True)
nx.draw_networkx_edges(subgraph, pos, width=[d['weight']*0.9 for (u, v, d) in subgraph.edges(data=True)])
plt.title('Karaliste Kelimeleri Arasındaki İlişki (Rastgele Alt Küme)')
plt.show()
```

## Makine Öğrenmesi Modeli <a name="makine-ogrenmesi-modeli"></a>

Destek Vektör Makineleri (SVM) kullanarak tweetlerin negatif ya da pozitif olarak sınıflandırılması yapılmıştır:

```python
from sklearn.model_selection import train_test_split
from sklearn.feature_extraction.text import TfidfVectorizer
from sklearn.svm import SVC
from sklearn.metrics import accuracy_score

df['Tip'] = df['Tip'].apply(lambda x: 1 if x == 'Negatif' else 0)

X = df['Paylaşım']
y = df['Tip']

X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.2, random_state=42)

vectorizer = TfidfVectorizer()
X_train_vec = vectorizer.fit_transform(X_train)
X_test_vec = vectorizer.transform(X_test)

model = SVC(kernel='linear', random_state=42)
model.fit(X_train_vec, y_train)

y_pred = model.predict(X_test_vec)
accuracy = accuracy_score(y_test, y_pred)
print(f"Model Doğruluğu: {accuracy}")
```

## Sonuçlar <a name="sonuclar"></a>

Model, tweetleri %85.34 doğruluk oranıyla sınıflandırmıştır.

## Dosyalar <a name="dosyalar"></a>

- `newcsv.csv`: İlk tweet veri seti.
- `karaliste.txt`: Küfür ve hakaret içeren kelimeler listesi.
- `son_hal.csv`: Ön işleme tabi tutulmuş veri seti.
- `karaliste_kullanim_sayilari.csv`: Karaliste kelimelerinin kullanım sayıları.
- `modelegitim.csv`: Model eğitimi için kullanılan veri seti.

Bu proje, Türkçe metinlerde küfür tespiti ve analizine yönelik bir örnek sunmaktadır. Verilerinizi yükleyerek ve kodu çalıştırarak benzer analizler yapabilirsiniz.
