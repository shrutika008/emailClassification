# emailClassification


import re 
class TextPreprocessor():

  def extract_email_ids(self,doc):
    '''This functions extract the email ids and domain names in the email adderss and returns a list of preprocessed email ids'''
    list_of_preproessed_emails = []
    list_of_emails = re.findall(r'\b[A-Za-z0-9._%+-]+@[A-Za-z0-9.-]+\.[A-Z|a-z]{2,}\b',doc)
    doc = re.sub(r'\b[A-Za-z0-9._%+-]+@[A-Za-z0-9.-]+\.[A-Z|a-z]{2,}\b'," ",doc)
    for txt in list_of_emails:
      email = re.split("[.]",re.split("@", txt)[1])
      y=email.copy()
      for i in email:
        if i=="com" or len(i)<=2:
          y.remove(i)
      email = ' '.join([str(i) for i in y])
      email = email.lower()
      list_of_preproessed_emails.append(email)
    list_of_preproessed_emails = " ".join(list_of_preproessed_emails)
    return list_of_preproessed_emails

  def text_lowercase(self,doc):
    ''' This function converts the text to lower case'''
    return doc.lower()

  def remove_digits(self, doc):
    '''This function removes all the numbers'''
    return re.sub('\d', '', doc)

  def remove_underscores(self, doc):
    '''This function removes all the underscores'''
    return re.sub(r'_', '', doc)

  def remove_excess_whitespace(self, doc):
    '''This function removes excess white spaces'''
    return re.sub('\s+', ' ', doc)

  def remove_special_characters(self, doc):
    '''This function removes all the special characters'''
    return re.sub('\W', ' ', doc)

  def remove_within_brackets(self, doc):
    '''This function removes all the content within brackets'''
    text = re.sub(r'
', '', doc)
    text = re.sub(r'<[^()]*>', '', text)
    return text

  def expand_words(self, phrase):
    '''This function expands the short form words '''
    phrase = re.sub(r"won't", "will not", phrase)
    phrase = re.sub(r"can\'t", "can not", phrase)
    phrase = re.sub(r"n\'t", " not", phrase)
    phrase = re.sub(r"\'re", " are", phrase)
    phrase = re.sub(r"\'s", " is", phrase)
    phrase = re.sub(r"\'d", " would", phrase)
    phrase = re.sub(r"\'ll", " will", phrase)
    phrase = re.sub(r"\'t", " not", phrase)
    phrase = re.sub(r"\'ve", " have", phrase)
    phrase = re.sub(r"\'m", " am", phrase)
    return phrase

  def remove_short_and_long_words(self, doc):
    '''This function removes all the short(<2 letters) and long(>15 letters) words '''
    words = doc.split()
    word_list = []
    for word in words:
      if len(word) > 2 and len(word) < 15 :
        word_list.append(word)
    return ' '.join(word_list)

  

  def text_lematizer(self,doc):
    '''This function lematize the words to its root words'''
    nlp = spacy.load('en', disable=['parser', 'ner'])
    doc = nlp(doc)
    return " ".join([token.lemma_ for token in doc])

  def clean_document(self,doc):
    '''This function cleans the documents'''
    doc = self.text_lowercase(doc)
    ids = self.extract_email_ids(doc)
    doc = self.remove_within_brackets(doc)
    doc = self.expand_words(doc)
    doc = self.remove_underscores(doc)
    doc = self.remove_special_characters(doc)
    doc = self.remove_digits(doc)
    doc = self.remove_excess_whitespace(doc)
    doc = self.remove_short_and_long_words(doc)
    doc = self.text_lematizer(doc)
    doc = ids+doc
    return doc
