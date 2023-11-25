# dasthoon
npx create-react-app comic-strip-generator
cd comic-strip-generator
// src/components/ComicForm.js
import React, { useState } from 'react';

const ComicForm = ({ onSubmit }) => {
  const [inputs, setInputs] = useState(Array(10).fill(''));

  const handleChange = (index, value) => {
    const newInputs = [...inputs];
    newInputs[index] = value;
    setInputs(newInputs);
  };

  const handleSubmit = (event) => {
    event.preventDefault();
    onSubmit(inputs);
  };

  return (
    <form onSubmit={handleSubmit}>
      {inputs.map((input, index) => (
        <input
          key={index}
          type="text"
          value={input}
          onChange={(e) => handleChange(index, e.target.value)}
          placeholder={`Panel ${index + 1}`}
        />
      ))}
      <button type="submit">Generate Comic</button>
    </form>
  );
};

export default ComicForm;
// src/api.js
const API_KEY = 'https://xdwvg9no7pefghrn.us-east-1.aws.endpoints.huggingface.cloud'; 

export const fetchComicImages = async (texts) => {
  try {
    const responses = await Promise.all(texts.map(text =>
      fetch('https://xdwvg9no7pefghrn.us-east-1.aws.endpoints.huggingface.cloud', {
        method: 'POST',
        headers: {
          'Authorization': `Bearer ${"https://xdwvg9no7pefghrn.us-east-1.aws.endpoints.huggingface.cloud"}`,
          'Content-Type': 'application/json',
        },
        body: JSON.stringify({ text }),
      })
    ));

    const images = await Promise.all(responses.map(res => res.json()));
    return images.map(img => img.url); // Assuming the response has an image URL
  } catch (error) {
    console.error('Error fetching images:', error);
    return [];
  }
};
// src/App.js
import React, { useState } from 'react';
import ComicForm from './components/ComicForm';
import { fetchComicImages } from './api';

const App = () => {
  const [comicImages, setComicImages] = useState([]);

  const handleFormSubmit = async (texts) => {
    const images = await fetchComicImages(texts);
    setComicImages(images);
  };

  return (
    <div>
      <ComicForm onSubmit={handleFormSubmit} />
      <div>
        {comicImages.map((url, index) => (
          <img key={index} src={url} alt={`Comic panel ${index + 1}`} />
        ))}
      </div>
    </div>
  );
};

export default App;
/* src/App.css */
form {
  display: flex;
  flex-direction: column;
  gap: 10px;
}

input[type="text"] {
  padding: 5px;
  margin: 5px 0;
}

img {
  max-width: 100%;
  height: auto;
  margin: 10px 0;
}
npm start
