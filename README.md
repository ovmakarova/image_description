# Текстовое описание объектов на изображениях 
## Image captioning
**Постановка задачи:** необходимо обучить сеть таким образом, чтобы она могла давать текстовое описание представленному изображению. Например: кошка лежит на лавке, человек держит телефон.
Работа сделала на основе NeuralTalk - это приложение, работающее по принципу нейронных сетей, основанное на разработках Стендфордского Университета и Google. 
NeuralTalk способно проанализировать комплексное изображение и точно определить, что на нём происходит, описав всё увиденное разговорным человеческим языком.

Входные данные представляют собой набор изображений и 5 предложений на английском языке, описывающие эти изображения. В частности мы используем базу с примерами Flickr8K (6000 изображений) и MSCOCO (82783 изображений). Для каждого изображения извлекается вектор CNN признаков с помощью 16-слойной VGG. 

# Архитектура
Модель основана на сверточной нейронной сети, которая работает с изображением в рекуррентной нейронной сети и преобразует его в компактное представление. А уже с помощью рекуррентной нейронной сети генерируется соответствующее предложение.
Задача разбивается на две стадии: 
1. Подпрограмма находит на фотографии объекты и классифицирует их на основе слов из существующего словаря. 
2. Другая подпрограмма подбирает описательные слова к разным регионам фотографии и затем объединяет их в предложения.

# Использованные модели
Для сравнения мы взяли обученные модели [здесь](http://cs.stanford.edu/people/karpathy/neuraltalk/). В обоих случаях для характеристики СNN используется VGGNet. Для генерации предложений был использован метод BeamSearch (последовательное рассмотрение наборов из beam лучших предложений). Для первой модели брали beam = 20, а для второй - beam = 1 (жадный поиск). LSTM обучается на данных COCO с 512 скрытым слоями.

# Эксперименты
В результате работы на выходе каждой сети получаем картинку с текстовым описанием и значение уверенности сети в результате.
Например:
![image](https://user-images.githubusercontent.com/13832465/31662538-694540c8-b347-11e7-8e9d-f01b1f11d3f1.png)

Если этот уровень низкий (например, -10), это означает, что модель путается с изображением и, скорее всего, не будет делать очень хороших прогнозов. И наоборот, более высокие цифры (такие как -7) указывают на то, что модель относительно более уверенна в исходе.

# Сравнение моделей
Для оценки работы моделей используется показатель, используемый в машинном переводе для оценки качества сгенерированных предложений – BLEU (Bilingual Evaluation Understudy). Он определяет процент n-грамм (n может быть от 1 до 7), совпавших в машинном переводе и эталонном переводе предложения. Вручную обычно оценивают по 5-балльной шкале два показателя: передача смысла (Adequacy) и гладкость речи (Fluency).

Подробнее результаты представлены в нашей презентации в папке presentation.

# Запуск программы
Основной код находится в файле `image_captioning.ipynb`. С помощью этой программы можно оценить изображения, которые лежит в папке `example_images`. В этой же папке в файле `tasks.txt` лежит список изображений и файл `vgg_feats.mat` с матрицей признаков для каждого изображения. 
По умолчанию код работает с моделью натренированной на базе Flickr8K. Но для наглядности результата мы поместили в папку резуьтат для двух наборов данных. Для того, чтобы самостоятельно протестировать результат на той или иной моделе необходимо поменять название файла. 
Результат работы сети сохраняется в JSON файл (result_struct.json) и имеет следующую структуру:
```
{
	"img_path": "mic.jpg",
	"candidate": {
		"text": "a group of people are posing for a picture",
		"logprob": -7.1806253800524704
	}
}
```
Путь до изображения, текст и уровень уверенности. Для наглядной визуализации результат так же записывается в html файл (`result.html`).

# Полезные ссылки

https://github.com/karpathy/neuraltalk

https://arxiv.org/pdf/1612.03557.pdf

https://tjournal.ru/55744-neuraltalk-recognize-photos

http://cs.stanford.edu/people/karpathy/deepimagesent/


