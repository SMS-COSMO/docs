## Rocket

## 数据存储

使用 json 文件存储数据，使用 serde_json 解析 json。

### `data.json` 文件格式

```json

```

- *data.rs*

打开数据文件（`data.json`）
```rust
use serde_json::Value;
use std::fs::File;
use std::fs::OpenOptions;
use std::io::prelude::*;

// Load data.json into serde_json::Value
pub fn open_data() -> Value {
    let f = std::fs::read_to_string("data.json");
    match f {
        Ok(str) => serde_json::from_str(str.as_str()).unwrap(),
        Err(_) => {
            let template_json: String =
                String::from("{\"feature_names\": [], \"paper\": []}");

            // Create file if not found
            let mut f1 = File::create("data.json").expect("failed to create data.json");
            f1.write_all(template_json.as_bytes())
                .expect("failed to write to data.json");

            serde_json::from_str(template_json.as_str()).unwrap()
        }
    }
}
```

写入 `data.json`
```rust
// Write data into data.json
pub fn write_data(data: Value) -> std::io::Result<()> {
    let mut f = OpenOptions::new()
        .write(true)
        .truncate(true)
        .open("data.json")
        .expect("failed to open data.json");
    f.write_all(data.to_string().as_bytes()).unwrap();

    Ok(())
}

```

读取停用词表（`stopwords.txt`），转为 `Vec<String>`。
```rust
// Get stopword list from stopwords.txt
pub fn get_stop_words<'a>() -> Vec<String> {
    let f = std::fs::read_to_string("stopwords.txt").unwrap();

    let mut words = vec![];
    for item in f.split_whitespace() {
        words.push(String::from(item));
    }

    words
}
```

## 分词

- *cut.rs*

使用 jieba 分词，并去除停用词。
```rust
use crate::data;
use jieba_rs::Jieba;

// Cut paper using jieba
pub fn cut<'a>(text: &'a String) -> Vec<&'a str> {
    let jieba = Jieba::new();
    let sep_list = jieba.cut(text.as_str(), false).to_vec();

    // Use stopwords
    let stop_words = data::get_stop_words();

    let mut res_list = vec![];
    for word in sep_list {
        if !stop_words.contains(&word.to_string()) {
            res_list.push(word);
        }
    }

    res_list
}
```

## Tf-idf

$$
\operatorname{tf-idf} = \operatorname{tf} \times \operatorname{idf}\\
\operatorname{idf} = 1 + \ln\frac{1 + n_d}{1 + \operatorname{df}}
$$

其中 $n_d$ 为文本总数。

- *process.rs*

更新 $\operatorname{df}$
```rust
// "n" -> "name"
// "d" -> "df"
pub fn update_feature_names(sep_text: Vec<&str>) -> () {
    let mut store = data::open_data();
    let names = store["feature_names"].as_array_mut().unwrap();

    for word in sep_text {
        let mut find = false;
        for i in 0..names.clone().len() {
            let name = &mut names[i];
            if name["n"].as_str().unwrap() == word {
                find = true;

                let df = name["d"].as_i64().unwrap();
                name["d"] = json!(df + 1);
                break;
            }
        }

        if !find {
            names.push(json!({"n": word, "d": 1}));
        }
    }

    data::write_data(store).unwrap();
}
```

获取 $\operatorname{tf}$
```rust
// "n" -> "name"
// "t" -> "tf"
pub fn get_tf_array(sep_text: Vec<&str>) -> Vec<Value> {
    let mut sorted_sep_text = sep_text.clone();
    sorted_sep_text.sort();

    let mut res: Vec<Value> = [].to_vec();

    let mut i = 0;
    while i < sorted_sep_text.len() {
        let mut cnt = sorted_sep_text.len() - i;
        for j in (i + 1)..sorted_sep_text.len() {
            if sorted_sep_text[j] != sorted_sep_text[i] {
                cnt = j - i;
                break;
            }
        }

        res.push(json!({"n": sorted_sep_text[i], "t": cnt}));
        i += cnt;
    }

    res
}
```

获取 $\operatorname{idf}$
```rust
// Get the tf-idf(smooth) characteristics
pub fn get_tf_idf_array(paper: Vec<Value>) -> Vec<f64> {
    let store = data::open_data();
    let names = store["feature_names"].as_array().unwrap();

    let mut res: Vec<f64> = vec![];
    let nd = names.len() as f64;

    for name in names {
        let mut found = false;
        for word in paper.clone() {
            if word["n"].as_str().unwrap() == name["n"].as_str().unwrap() {
                // tf-idf = tf * idf
                // idf(smooth) = ln((1 + nd) / (1 + df)) + 1

                let tf = word["t"].as_f64().unwrap();
                let idf = 1.0 + f64::ln((1.0 + nd) / (1.0 + name["d"].as_f64().unwrap()));

                res.push(tf * idf);
                found = true;
                break;
            }
        }

        if !found {
            res.push(0.0);
        }
    }

    res
```

## 余弦相似度

$$
\cos\theta = \frac{\sum_{i=1}^na_i\times b_i}{\sqrt{\sum_{i=1}^n(a_i)^2}\times \sqrt{\sum_{i=1}^n(b_i)^2}}
$$

- *process.rs*
```rust
pub fn cosine_similarity(v_a: Vec<f64>, v_b: Vec<f64>) -> f64 {
    let mut product_sum = 0.0;
    let mut a_square_sum = 0.0;
    let mut b_square_sum = 0.0;

    for i in 0..v_a.len() {
        product_sum += v_a[i] * v_b[i];
        a_square_sum += v_a[i].powi(2);
        b_square_sum += v_b[i].powi(2);
    }

    product_sum / (a_square_sum.sqrt() * b_square_sum.sqrt())
}
```
