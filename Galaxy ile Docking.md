---
author: "alper yilmaz"
title: "Galaxy ile Docking"
tags: presentation
---

# Galaxy ile Docking

*İVEK Eğitim, 1-2 Aralık 2021*
*Hazırlayan: Selcen ARI YUKA*

Bu materyale [https://bit.ly/ivek-docking](https://bit.ly/ivek-docking) adresinden ulaşılabilir.

## SARS-CoV-2 Spike Glycoprotein'e Karşı Kimyasal Molekül Tespiti



### **Spike Glycoprotein**

<img style="float: right;" align="right" width="150" height="50" src="https://cdn.rcsb.org/rcsb-pdb/v2/common/images/rcsb_logo.png">

- Proteinin kristal yapısı: PDB ([Protein Data Bank](https://www.rcsb.org/)).
- Arama kutucuğuna docking yapılacak proteinin tanımlayıcısı yazılarak istenilen protein yapısı incelenebilir.



SARS-CoV-2 S proteini : **6VXX**

<img align="center" width="500" height="250" src="https://i.imgur.com/STcrnbA.png">


---
 
### Galaxy Docking Özet

:::info
1. Yeni 'History' Oluşturma
2. Protein Yapısını (S Glikoproteini) PDB'den Ortama Yükleme
3. Protein (Reseptör) Üzerinde Kavite (Etkileşim Bölgesi) Tahmini
4. Ligand (Molekül) Kütüphanesi Yükleme
5. Aday Moleküllerin Veri Formatlarının Düzenlenmesi
6. Aday Moleküller ile Protein Etkileşiminin Tahmini (Docking)
7. Uygun Ligandın Seçimi
8. Görüntüleme 
:::


:::spoiler
https://training.galaxyproject.org/training-material/topics/computational-chemistry/tutorials/cheminformatics/tutorial.html#prepare-files-for-docking
:::


---
### 1. Yeni History Oluşturma

![](https://i.imgur.com/ySbk1iB.png)

---
### 2. Protein Yapısını (S Glikoproteini) PDB'den Ortama Yükleme

:::success
* **Get PDB file** tool'u aranır. 
* **PDB accession code**: **6VXX** olarak belirtilir.
* PDB üzerinden arama tamamlandıktan sonra PDB dosyası *S_glycoprotein* olarak isimlendirilir.
* Veri tipinin *pdb* olduğundan emin olunmalıdır.
:::


<img align="center" width="700" height="350" src="https://i.imgur.com/fvl44Xq.png">

:::spoiler

**ChemicalToolBox** kütüphanesinden **Get PDB file** tool'u sol konsoldaki *search tools* aracılığı ile bulunabilir. Tool'u kullanmak için üzerine tıklayınız. 

Veri isimlendirilmesi ve veri tipinin kontrolü için, işlem paketinde (sağ konsol), **Edit attributes** (kalem) ikonunu kullanınız. 

<img align="center" width="350" height="200" src="https://i.imgur.com/fvuwIOS.png">
:::


---
### 2. Protein Yapısını (S Glikoproteini) PDB'den Ortama Yükleme: **Reseptörü Docking için Hazırlama**

Autodock Vina aracı, *pdb* dosyalarını doğrudan kullanamaz. Bu nedenle **PDB -> PDBQT** dosya dönüşümünün yapılması gerekir.

:::success
* **ChemicalToolBox** kütüphanesindeki **Prepare receptor** tool'u aranır. 
* **Select a PDB file**: *S_glycoprotein* PDB dosyası seçilir.
* Dosya yüklendikten sonra *S_glycoprotein PDB QT* olarak isimlendirilir.
* Veri tipinin *pdbqt* olduğundan emin olunmalıdır.
:::

![](https://i.imgur.com/btRMed5.png)


:::spoiler
PDBQT dosyası AutoDock atom türlerine uyacak şekilde değiştirilmiş atom türü ve eklenen Gasteiger yüklerini içeren ekstra bir sütunla, PDB'ye benzer bir biçime sahiptir.

*Gasteiger-Marsili*: empirical atomic partial charges

![](https://i.imgur.com/TOb9xXA.png)


:::

---
### 3. Protein (Reseptör) Üzerinde Kavite (Etkileşim Bölgesi) Tahmini

Docking için kullanılacak dosya; 

- Ligand-protein kompleksini içeriyorsa:

:::info
Protein ve ligandın birlikte bulunduğu PDB dosyasında ligand tanımlayıcı değişkenler aracılığıyla ligand dosyası ayıklanır.
:::

- Yalnızca **PROTEİN** içeriyorsa:

<img style="float: right;" align="right" width="200" height="50" src="https://i.imgur.com/8f5HBTV.png">

:::info
Proteinin herhangi bir diğer molekülle hangi aktif bölgeden **(kavite veya pocket)** etkileşebileceğini tahmin eden tool'lar kullanılır.
:::

---

### 3. Protein (Reseptör) Üzerinde Kavite (Etkileşim Bölgesi) Tahmini

:::success
- **ChemicalToolBox** kütüphanesinden **fpocket** tool'u aranır.
- **Girdi dosyası**: *S_glycoprotein* PDB dosyası seçilir.
- **Type of pocket to detect** parametresi *Small molecule binding sites* olarak belirlenir.
- **Output files**: *PDB files containing the atoms in contact with each pocket* ve *Log file containing pocket properties* opsiyonları seçilir.
:::

![](https://i.imgur.com/1vDYc5d.png)


Bu aşama iki tipte dosya çıktısı elde edilir.

1. Tüm kaviteler (pocket) için PDB dosyaları
2. Kavitelerin özelliklerini içeren *txt* dosyaları

**Not:** *Pocket properties txt* dosyasında kaviteler olasılıklarına göre derecelendirilmiştir.

---

*Pocket PDB* dosyası örneği

![](https://i.imgur.com/Bsi5hka.png)

---

*Pocket properties* dosyası genel görünümü

![](https://i.imgur.com/McRop3h.png)

---

### 3. Protein (Reseptör) Üzerinde Kavite (Etkileşim Bölgesi) Tahmini: **Seçilen Kavitenin Docking için Düzenlenmesi**

Proteinin seçilen kavitesi **box parameter**'lerinin hesaplanması gerekmektedir.

:::success
- **ChemicalToolBox** kütüphanesi kullanılarak **Calculate the box parameters using RDKit** seçilir ve aşağıdaki parametreler ile çalıştırılır:
--**Input ligand or pocket**: *Atoms in contact with each pocket* koleksiyonundan **pocket1**
--**x-axis buffer**: *5*
--**y-axis buffer**: *5*
--**z-axis buffer**: *5*
--**Exhaustiveness (optional)**: *1*
--**Random seed**: *1*
- İş paketi çalıştırıldıktan sonra çıktı dosyası *Docking config file derived from pocket 1* olarak isimlendirilir.
:::

![](https://i.imgur.com/fuTW6da.png)

:::spoiler
Girdi dosyası olarak **pocket1** seçimi, arama çubuğunda sağda konumlanmış olan **Browse Dataset** ikonu (dosya) ile gerçekleştirilmektedir.
:::

---

### 4. Ligand (Molekül) Kütüphanesi Yükleme

Aday moleküllerin etkileşimlerinin bulunabilmesi için **Compound Library** derlenmesi gerekmektedir.

12 aday molekül için *smi* dosyasının yüklenmesi gerekir:

:::info
```

CCN(CC)S(=O)(=O)N1CCOC(C1)c1c(sc2ncccc12)C(=O)NC(C)C	258603
CC(CN1CCC(C1)c1cc2nc(C)c(C)c(O)n2n1)c1ccccc1	346554
CC(=O)N1CCC(C)(CC1)c1cc(nc(C)n1)C(=O)NCC1(C)CCCO1	255441
Cc1nc2cc(nn2c(O)c1CCC(=O)N1CCCCC1)C1CCCN1C1CCOCC1	153681
CN(C)C(=O)C1(C)CN(Cc2cccc3[nH]ccc23)CCO1	206377
CC12CCC(CC1C(=O)NCc1ccc(F)cc1)[C@]1(C2)NC(=O)c2ccccc2N1	317290
CS(=O)(=O)c1cn[nH]c1CC1CCCN(Cc2cccs2)C1	245385
Fc1cccc(Cc2cccc(n2)C2CCCN(C2)C(=O)Cc2ccccc2)c1	150473
CC(C)NC(=O)C1(Cc2ccc(cc2)-c2cccs2)CCN(C1)C(=O)Cn1cccn1	393314
CC(C)C(=O)N1CCC(CC2CC(=NO2)c2ccccc2)(CC1)C(=O)NCC=C	366222
O=C(CN1CCC(CC1)c1ccc2OCOc2c1)N1CCCc2ccccc12	291523
Fc1ccc(CC2(CCOCC2)C(=O)N2C3CCC2CC(Cc2ccnc4[nH]ccc24)C3)cc1	283894
```
:::

:::spoiler
**Not:** Hızlı bir docking işlemi geçekleştirmek için yukarıda verilen aday moleküllerden 12'sini içeren *sampled_molecules.smi* dosyası tarafımızdan temin edilecektir.

Aşağıdaki dosya linkini kullanabilirsiniz:
`https://raw.githubusercontent.com/alperyilmaz/ivek-2021/main/sampled_molecules.smi`
:::

:::success
- **Upload Data**: *Choose local files* seçeneği ile **sampled_molecules.smi** dosyası seçilir ve yükleme başlatılır.
- Yükleme bitikten sonra **Edit attributes** menüsünden dosya adı **compound_library** olarak değiştirilir ve veri tipinin *smi* olduğu kontrol edilir.
:::


---
### 5. Aday Moleküllerin Veri Formatlarının Düzenlenmesi

**compound_library** veri seti *smi* veri tipindedir. Docking işlemi için moleküllerin **sdf** formatına dönüştürülmesi gerekir.

:::success
- **ChemicalToolBox** kütüphanesi kullanılarak **Compound conversion** tool'u seçilir ve aşağıdaki parametreler ile çalıştırılır:
    - **Molecular input file**: *compound_library*
    - **Output Format**: *MDL MOL format (sdf, mol)* seçilir
    - **Generate 3D coordinates**: *Yes*
    - **Add hydrogens appropriate for pH**: *7.4*
- İş paketi çalıştırıldıktan sonra çıktı dosyası *Prepared Ligands* olarak isimlendirilir.
:::

![](https://i.imgur.com/o5507S7.png)


---

### 6. Aday Moleküller ile Protein Etkileşiminin Tahmini (Docking)

:::success
- **ChemicalToolBox** kütüphanesi kullanılarak **VINA Docking** tool'u seçilir ve aşağıdaki parametreler ile çalıştırılır:
-- **Receptor**: *S_glycoprotein PDB QT* dosyası seçilir.
-- **Ligands**: *Prepared Ligands*
-- **Specify pH value for ligand protonation**: *7.4*
-- **Specify parameters**: *Upload a config file to specify parameters*
-- **Box configuration**: *Docking config file derived from pocket 1*

:::

![](https://i.imgur.com/mhSKJ0W.png)

İşlem sonrasında **Docked ligands for data ...** isimli veri seti koleksiyonu **Docked_ligands** olarak değiştirilir. Koleksiyon isminin seçilmesinden sonra *click to rename collection* opsiyonu görülecektir.


### 7. Uygun Ligandın Seçimi

Autodock Vina programında yapılan analizin sonucunda, ligand-reseptör etkileşimlerinin affinite (enerji) değerlerinin kıyaslanması yoluyla en uygun aday ligandlar (moleküller) belirlenir.

Bunun için öncelikle çıktı dosyalarının koleksiyondan veri seti haline getirilmesi gerekir.

:::success
- **Extract values from an SD-file** tool'u aranır ve seçilir.
- Girdi dosyası **SD-file**: Dataset collection butonu kullanılarak *Docked ligands* koleksiyonu seçilir.
- **Include the property name as header**: *Yes*
- **Include SMILES as column in output**: *Yes*
- **Include molecule name as column in output**: *Yes*
:::

![](https://i.imgur.com/O9iGVqY.png)

Bu iş paketi 12 SD-file'dan önemli alanları *tabular* formda çıkarır. Sonrasında tüm *tabular* veri setleri aşağıdaki şekilde birleştirilir:

:::success
- **Collapse Collection** tool'u seçilir.
- Girdi dosyası **Collection of files to collapse into dataset**: Dataset collection butonu kullanılarak *Extract values from an SD-file* tabular veri seti koleksiyonu seçilir.
- **Keep one header line**: *Yes*
:::

![](https://i.imgur.com/ZItay4E.png)

Bir araya derlenen dosyalar aşağıdaki şekilde derlenebilir:

:::success
- **Sort** tool'u seçilir.
- **Sort Dataset**: *Collapse Collection* çıktı dosyası olarak belirlenir.
- **on column**: *Column:5* SCORE sütununa karşılık gelmektedir.
- **with flavor**: *Numerical sort*
- **everything in**: *Ascending order*
![](https://i.imgur.com/wyvlmN5.png)
- Çalıştırıldıktan sonra **Edit attributes** menüsü kullanılarak veri seti ismi *Sorted_ligand_interactions* olarak değiştirilir.
:::

![](https://i.imgur.com/TIUuQmH.png)

Autodock Vina programında *SCORE* değişkeni en negatif olan bağlanma en iyi etkileşimin görüldüğü bağlamadır. İlk 10 pozun bulunduğu resimden (yukarıda) SARS-CoV-2 S glikoproteini için en uygun küçük molekün **283894** olduğu görülmektedir.


### 8. Görüntüleme

Ligand-reseptör etkileşimlerinin görsel olarak da izlenebilmesi için farklı araçlar mevcuttur. Ancak öncelikle dosyaların doğru şekilde hazırlanması gerekir.

**Tip**: Docking aşamasında üretilen **SD-file** verilerinin PDB formuna dönüştürülmesi gerekir. Öncelikle **Docked ligands** sdf dosyalarından **283894** ligandı için dosya ismi bulunur:

**ligand12_docked**

:::success
- **Compound conversion** tool'u seçilir.
- **Molecular input file**: *ligand12_docked*
- **Output format**: *Protein Data Bank format (pdb)*
- **Split multi-molecule files into a collection**: *Yes*
:::

![](https://i.imgur.com/Tz6jC1O.png)

Çıktı dosyası [NGL viewer](http://nglviewer.org/ngl/) aracına .pdb dosyalarının yüklenmesi ile görüntülenebilir.

- *S_glikoprotein.pdb* dosyasını ve en uygun ligand molekülünün pdb dosyasını (önceki iş paketinde elde edilmiştir) indiriniz. Dilerseniz aşağıdaki adresten dosyaları indirebilirsiniz. [Link1](https://github.com/alperyilmaz/ivek-2021/raw/main/galaxy-docking-results.zip)
  ```
  https://raw.githubusercontent.com/alperyilmaz/ivek-2021/main/galaxy-ligand12-docked.pdb
  https://raw.githubusercontent.com/alperyilmaz/ivek-2021/main/galaxy-S_glycoprotein.pdb
  ```

- [NGL viewer](http://nglviewer.org/ngl/)'ı açınız ve bu dosyaları NGL viewer ortamına yükleyiniz.

![](https://i.imgur.com/BAtXOpu.png)

- Görüntülenmek istenen ligandın, *S_glikoprotein.pdb* dosyası ile birlikte gelen ligandlardan ayrılabilmesi için diğer tüm ligandlar şeffaf hale getirilebilir.

![](https://i.imgur.com/hkGbQwi.png)

- Ligandın daha belirgin görünebilmesi için S glikoproteini istenilen miktarda şeffaf hale getirilebilir.

![](https://i.imgur.com/wjfgJtq.png)

- İstenilen en iyi görselleştirme için fare yardımıyla protein-ligand kompleksi, büyütülüp küçültülebilir, farklı açılardan görüntülemek için döndürülebilir ve diğer görselleştirme ayarlarından yararlanılabilir. 

![](https://i.imgur.com/leIXx3x.png)
